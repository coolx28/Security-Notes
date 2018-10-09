---
description: Exploring ways to detect Sysmon presence on the victim system
---

# Detecting Sysmon on the Victim Host

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

## Get-SysmonConfiguration

A powershell tool by @mattifestation that extracts sysmon rules from the registry:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
PS C:\tools> (Get-SysmonConfiguration).Rules
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-10-09-18-12-09.png)

As an example, looking a bit deeper into the `ProcessCreate` rules:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
(Get-SysmonConfiguration).Rules[0].Rules
```
{% endcode-tabs-item %}
{% endcode-tabs %}

We can see the rules almost as they were presented in the sysmon configuration XML file:

![](../.gitbook/assets/screenshot-from-2018-10-09-18-13-37.png)

A snippet from the actual sysmonconfig-export.xml file:

![](../.gitbook/assets/screenshot-from-2018-10-09-18-14-57.png)

## Bypassing Sysmon

Since [Get-SysmonConfiguration](detecting-sysmon-on-the-victim-host.md#get-sysmonconfiguration) gives you the ability to see the rules sysmon is monitoring on, you can play around those.

Another way to bypass the sysmon altogether is explored here:

{% page-ref page="unloading-sysmon-driver.md" %}

{% embed url="https://www.darkoperator.com/blog/2018/10/5/operating-offensively-against-sysmon" %}

{% embed url="https://github.com/mattifestation/PSSysmonTools/blob/master/PSSysmonTools/Code/SysmonRuleParser.ps1" %}

{% embed url="https://docs.microsoft.com/en-us/windows-hardware/drivers/ifs/allocated-altitudes" %}

{% embed url="https://github.com/GhostPack/Seatbelt" %}



