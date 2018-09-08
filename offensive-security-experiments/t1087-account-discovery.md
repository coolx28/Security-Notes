---
description: Discovery
---

# T1087: Account Discovery & Enumeration

## Execution

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
net user
net user administrator
whoami /user
whoami /all
...
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

Having command line logging can help identifying a cluster of enumeration commands used in a relatively short span of time.

For this lab, I exported 8600+ commandlines from various proceeses and wrote a dirty powershell script that ingests those commandlines and inspects them for a couple of classic windows enumeration commands that are executed in the span of 2 minutes and spits them out:

{% code-tabs %}
{% code-tabs-item title="hunt.ps1" %}
```csharp
function hunt() {
    [CmdletBinding()]Param()
    $commandlines = Import-Csv C:\Users\mantvydas\Downloads\cmd-test.csv
    $watch = 'whoami|net1 user|hostname|netstat|net localgroup|cmd /c'
    $matchedCommandlines = $commandlines| where-object {  $_."event_data.CommandLine" -match $watch}

    $matchedCommandlines| foreach-Object {
        [datetime]$eventTime = $_."@timestamp"
        [datetime]$low = $eventTime.AddSeconds(-60)
        [datetime]$high = $eventTime.AddSeconds(60)
        $clusteredCommandlines = $commandlines | Where-Object { [datetime]$_."@timestamp" -ge $low -and [datetime]$_."@timestamp" -le $high -and  $_."event_data.CommandLine" -match $watch}
        
        if ($clusteredCommandlines.length -ge 4) {
            Write-Verbose "Possible enumeration around time: $low - $high ($eventTime)"
            $clusteredCommandlines
        }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Invoking the script to start the hunt:

```csharp
. \hunt.ps1; hunt -verbose
```

Below are some of the findings which you may warrant further investigation to see what the system was up to at those points in time:

![](../.gitbook/assets/enumeration-hunt-5.png)

![](../.gitbook/assets/enumeration-hunt-4.png)

![](../.gitbook/assets/enumeration-hunt-3.png)

![](../.gitbook/assets/enumeration-hunt-2.png)

![](../.gitbook/assets/enumeration-hunt-1.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1087\",\"type\":\"link\",\"title\":\"Account Discovery - ATT&CK for Enterprise\"}" %}

