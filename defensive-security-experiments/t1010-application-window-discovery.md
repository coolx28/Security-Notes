---
description: Discovery
---

# T1010: Application Window Discovery

## Execution

Retrieving running application window titles:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
get-process | where-object {$_.mainwindowtitle -ne ""} | Select-Object mainwindowtitle
```
{% endcode-tabs-item %}
{% endcode-tabs %}



## Observations

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1010\",\"type\":\"link\",\"title\":\"Application Window Discovery - ATT&CK for Enterprise\"}" %}



