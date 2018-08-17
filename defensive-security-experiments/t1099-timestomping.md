---
description: Defense Evasion
---

# T1099: Timestomping

## Execution

```csharp
.\timestomp.exe .\nc.exe -v
.\RawCopy64.exe /FileNamePath:C:\$MFT /OutputName:c:\experiments\mft.dat
.\timestomp.exe .\nc.exe -c "Monday 7/25/2005 5:15:55 AM"
Import-Csv .\mft.csv -Delimiter "`t" | Where-Object {$_.Filename -eq "nc.exe"}

```

![](../.gitbook/assets/timestomp-original.png)

![](../.gitbook/assets/timestomp-forged.png)

![](../.gitbook/assets/timestomp-dump-parse-mft.png)

![](../.gitbook/assets/timestomp-mft-timestamps.png)

{% embed data="{\"url\":\"https://www.forensicswiki.org/wiki/Timestomp\",\"type\":\"link\",\"title\":\"Timestomp - ForensicsWiki\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.forensicswiki.org/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1099\",\"type\":\"link\",\"title\":\"Timestomp - ATT&CK for Enterprise\",\"icon\":{\"type\":\"icon\",\"url\":\"https://attack.mitre.org/favicon.ico\",\"aspectRatio\":0}}" %}

