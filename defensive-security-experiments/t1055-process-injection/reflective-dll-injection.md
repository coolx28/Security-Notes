---
description: Loading DLL from memory rather than disk.
---

# Reflective DLL Injection

This is a DLL injection technique where a malicious binary writes some evil DLL's contents into a remote \(victim\) process and makes the victim process load that DLL and execute malicious code contained by that evil DLL.

This is done in a series of steps, but one of key pieces is that Process Environment Block is leveraged of the victim's process in order to find the required loaded modules such as `kernel32` and then find its exported functions such as `LoadLibrary`, `GetProcAddress` and `VirtualAlloc`.

For our exploration of the PEB:

{% page-ref page="../../memory-forensics/process-environment-block.md" %}

## Execution

This lab assumes that the attacker has already gained a meterpreter shell from a victim system and the attacker now will attempt to inject a reflective DLL that is sitting on the attackers disk into a process on a compromised victim system, more specifically into a notepad.exe process with PID 6156.

Metasploit's post-exploitation module `windows/manage/reflective_dll_inject` configured:

![](../../.gitbook/assets/reflective-dll-options%20%281%29.png)

After executing the post exploitation module, the below graphic shows how the notepad.exe executes the malicious payload that came from a reflective DLL that was sent over the wire from the attacker's system:

![](../../.gitbook/assets/reflective-dll-gif.gif)

## Observations



