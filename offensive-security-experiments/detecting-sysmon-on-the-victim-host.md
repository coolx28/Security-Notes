---
description: Exploring ways to detect Sysmon presence on the victim system
---

# Detecting Sysmon on the Victim Host

Once landed, on a victim machine, attackers may want to check if sysmon is installed on the system.

## Processes

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
PS C:\> Get-Process | Where-Object { $_.ProcessName -eq "Sysmon" }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-10-09-17-39-28.png)

{% hint style="warning" %}
Note: process name can be changed during installation
{% endhint %}

## Services

```csharp
Get-CimInstance win32_service -Filter "Description = 'System Monitor service'"
# or
Get-Service | where-object {$_.DisplayName -like "*sysm*"}
```

![](../.gitbook/assets/screenshot-from-2018-10-09-17-48-11.png)

{% hint style="warning" %}
Note: display names and descriptions can be changed
{% endhint %}

## Windows Events

```csharp
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Channels\Microsoft-Windows-Sysmon/Operational
```

![](../.gitbook/assets/screenshot-from-2018-10-09-17-50-47.png)

## Filters

```text
PS C:\> fltMC.exe
```

Note how even though you can change the service name and the driver name, the sysmon altitude is always the same - `385201`

![](../.gitbook/assets/screenshot-from-2018-10-09-17-51-45.png)

## Sysmon Tools + Accepted Eula

```text
ls HKCU:\Software\Sysinternals
```

![](../.gitbook/assets/screenshot-from-2018-10-09-17-56-33.png)

## Bypass?

Take a look at ways to bypass the sysmon:

{% page-ref page="unloading-sysmon-driver.md" %}

{% embed url="https://github.com/mattifestation/PSSysmonTools/blob/master/PSSysmonTools/Code/SysmonRuleParser.ps1" %}



