---
description: 'UAC Bypass/Defense Evasion, Persistence'
---

# T1122: COM Hijacking

## Execution

On the compromised system, change the `HKEY_LOCAL_MACHINE\SOFTWARE\Classes\mscfile\shell\open\command` default value to point to your binary. In this case I chose powershell.exe:

![](../.gitbook/assets/com-registry.png)

By default, launching Windows Event Viewer calls under the hood:`"C:\Windows\system32\mmc.exe" "C:\Windows\system32\eventvwr.msc" /s` 

Since we hijacked the `HKEY_LOCAL_MACHINE\SOFTWARE\Classes\mscfile\shell\open\command` to point to powershell, when launching Even Viewer, the powershell is invoked instead:

![](../.gitbook/assets/com-powershell.png)

## Observation

Monitoring registry for changes in `HKEY_CLASSES_ROOT\mscfile\shell\open\command` can reveal this hijaking activity:

![](../.gitbook/assets/com-sysmon.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1122" %}

{% embed url="https://enigma0x3.net/2016/08/15/fileless-uac-bypass-using-eventvwr-exe-and-registry-hijacking/" %}

