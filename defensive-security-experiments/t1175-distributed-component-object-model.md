---
description: Lateral Movement
---

# T1175: Distributed Component Object Model

This lab explores a DCOM lateral movement technique using MMC20.Application COM as originally researched by enima0x3 in his blog post [Lateral Movement using the mmc20.application Com Object](https://enigma0x3.net/2017/09/11/lateral-movement-using-excel-application-and-dcom/)

## Execution

```csharp
Get-ChildItem 'registry::HKEY_CLASSES_ROOT\WOW6432Node\CLSID\{49B2791A-B1AE-4C90-9B8E-E860BA07F889}'
```



## Observations

{% embed data="{\"url\":\"https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/\",\"type\":\"link\",\"title\":\"Lateral Movement using the MMC20.Application COM Object\",\"description\":\"For those of you who conduct pentests or red team assessments, you are probably aware that there are only so many ways to pivot, or conduct lateral movement to a Windows system. Some of those techn…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://enigma0x3.files.wordpress.com/2017/01/process\_validation.png\",\"width\":1416,\"height\":598,\"aspectRatio\":0.422316384180791}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows/desktop/com/com-technical-overview\",\"type\":\"link\",\"title\":\"COM Technical Overview\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1175\",\"type\":\"link\",\"title\":\"Distributed Component Object Model - ATT&CK for Enterprise\"}" %}

