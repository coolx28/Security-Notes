---
description: 'Persistence, code execution using netsh helper arbitrary libraries.'
---

# T1128: NetSh Helper DLL

## Execution

[NetshHelperBeacon helper DLL](https://github.com/outflanknl/NetshHelperBeacon) will be used to test out this technique - it can be downloaded below:

{% file src="../.gitbook/assets/netshhelperbeacon.dll" caption="NetshHelperBeacon" %}

The helper library, once loaded, will start `calc.exe`:

![](../.gitbook/assets/netsh-code%20%281%29.png)

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
.\netsh.exe add helper C:\tools\NetshHelperBeacon.dll
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/netsh-calc.png)

## Observations

Adding the helper via commandline adds a registry key for the new helper, so you may want to monitor for registry changes in `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NetSh`

![](../.gitbook/assets/netsh-registry.png)

When netsh gets executed, Procmon captures the evil DLL being loaded as new thread is being created by the netsh because of the evil DLL:

![](../.gitbook/assets/netsh-procmon.png)

As usual, monitoring command line arguments is a good idea that may help uncover suspicious activity:

![](../.gitbook/assets/netsh-logs1.png)

![](../.gitbook/assets/netsh-logs2.png)

## Interesting

Loading the helper DLL crashed the netsh \(did not get to the root cause of the issue\). Inspecting the calc.exe process after the crash with Process Explorer reveals that the parent process is svchost, although the sysmon logs showed cmd.exe as its parent:

![](../.gitbook/assets/netsh-ancestry.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1128\",\"type\":\"link\",\"title\":\"Netsh Helper DLL - ATT&CK for Enterprise\"}" %}

