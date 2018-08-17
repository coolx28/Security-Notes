---
description: Defense Evasion
---

# T1099: Timestomping

## Execution

Checking original timestamps of the `nc.exe`:

```csharp
.\timestomp.exe .\nc.exe -v
```

![](../.gitbook/assets/timestomp-original.png)

Forging the file creation date:

```csharp
.\timestomp.exe .\nc.exe -c "Monday 7/25/2005 5:15:55 AM"
```

![](../.gitbook/assets/timestomp-forged.png)

Checking the `$MFT` for changes - first of, dumping the `$MFT`:

```csharp
.\RawCopy64.exe /FileNamePath:C:\$MFT /OutputName:c:\experiments\mft.dat
```

![](../.gitbook/assets/timestomp-dump-parse-mft.png)

Let's find the `nc.exe` record and check its times:

```csharp
Import-Csv .\mft.csv -Delimiter "`t" | Where-Object {$_.Filename -eq "nc.exe"}
```

![](../.gitbook/assets/timestomp-mft-timestamps.png)

{% embed data="{\"url\":\"https://www.forensicswiki.org/wiki/Timestomp\",\"type\":\"link\",\"title\":\"Timestomp - ForensicsWiki\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.forensicswiki.org/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1099\",\"type\":\"link\",\"title\":\"Timestomp - ATT&CK for Enterprise\",\"icon\":{\"type\":\"icon\",\"url\":\"https://attack.mitre.org/favicon.ico\",\"aspectRatio\":0}}" %}

