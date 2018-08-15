---
description: Defense Evasion
---

# T1027: Obfuscated Powershell Invocations

This topic is huge, but in this lab, I wanted to see if I could do a simple \(maybe a bit overly simplistic, but I think it shows the concept OK\) hunt for encoded powershell command invocations.

## Defining the Hunt

I want to find processes with base64 encoded commandlines that may indicate obfuscated powershell invocations.

Data sources: process creation commandline arguments provided by sysmon logs.

## Execution

I had a sample of 27000 events that had a commandline logged, which I exported to a .csv file:

![](../.gitbook/assets/kibana-cmdlines.png)

Since malicious encoded commands are usually lenghty, contiguous sequence of printable ASCII characters \(including characters such as =,/,+\), I decided to loop through the commandlines and only pull those that matched a simple regex `([A-Za-z0-9]){64,}`

Full powershell oneliner below:

```csharp
Import-Csv .\cmdline.csv | Where-Object {$_."event_data.CommandLine" -match '([A-Za-z0-9]){64,}' }  | ForEach-Object { Write-Output $_.'event_data.CommandLine'; Write-host }
```

Below are the results - note how out of 27000+ events, only a handful were returned, among which was one base64 encoded powershell commandline:

![](../.gitbook/assets/powershell-outlier.png)

Since I am looking for malicious powershell invocations, I could adjust the query as follows to remove processes that do not contain `powershell.exe` mentioned in them:

```csharp
Import-Csv .\cmdline.csv | Where-Object {$."event_data.CommandLine" -match '([A-Za-z0- 9]){64,}' -and $."eventdata.CommandLine" -match 'powershell.exe' } | ForEach-Object { Write-Output $.'event_data.Comm andLine'; Write-host }
```

Bingo - only one result returned:

![](../.gitbook/assets/powershell-single.png)

This type of hunting is interesting, so I will be coming back to similar topics to explore this domain further.

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1027\",\"type\":\"link\",\"title\":\"Obfuscated Files or Information - ATT&CK for Enterprise\"}" %}





