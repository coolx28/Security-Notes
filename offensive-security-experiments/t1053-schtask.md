---
description: 'Code execution, privilege escalation, lateral movement and persitence.'
---

# T1053: Schtask

## Execution

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
schtasks /create /sc minute /mo 1 /tn "eviltask" /tr C:\tools\shell.cmd /ru "SYSTEM"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

Note that processes spawned as scheduled tasks have `taskeng.exe` process as the parent:

![](../.gitbook/assets/schtask-ancestry.png)

Monitoring and inspecting commandline arguments and established network connections by processes can help uncover suspicious activity:

![](../.gitbook/assets/schtasks-created.png)

![](../.gitbook/assets/schtask-connection.png)

Also, look for vents 4698 indicating new scheduled task creation:

![](../.gitbook/assets/schtasks-created-new-task.png)

### Lateral Movement

Note that when using schtasks for lateral movement, the processes spawned do not have taskeng.exe as their parent, rather - svchost:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
schtasks /create /sc minute /mo 1 /tn "eviltask" /tr calc /ru "SYSTEM" /s dc-mantvydas /u user /p password
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/schtasks-remote.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1053" %}

{% embed url="https://docs.microsoft.com/en-us/windows/desktop/taskschd/schtasks" %}

