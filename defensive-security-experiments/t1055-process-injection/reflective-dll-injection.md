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

Once the metasploit's post-exploitation module is run, the procmon accurately registers that notepad created a new thread:

![](../../.gitbook/assets/reflective-dll-injection-new-thread.png)

Let's see if we can locate where the contents of `reflective_dll.x64.dll` are injected into the victim process when the metasploit's post-exploitation module executes.

For that, in WinDBG, let's set up a breakpoint for `MessageBoxA` as shown below and run the post-exploitation module again:

```cpp
0:007> bp MessageBoxA
0:007> bl
0 e 00000000`77331304     0001 (0001)  0:**** USER32!MessageBoxA
```

The breakpoint is hit:

![](../../.gitbook/assets/reflective-dll-bp-hit.png)

At this point, we can inspect the stack with `kv` and see the call trace. A couple of points to note here:

* return address the code will jump to after the `USER32!MessageBoxA` finishes is `00000000031e103e`
* inspecting assembly instructions around `00000000031e103e`, we see a call instruction `call qword ptr [00000000031e9208]`
* inspecting bytes stored in `00000000031e9208`, \(`dd 00000000031e9208 L1`\) we can see they look like a memory address `0000000077331304` \(note this address\)
* inspecting the EIP pointer \(`r eip`\) where the code execution is paused at the moment, we see that it is the same `0000000077331304` address, which means that the earlier mentioned instruction `call qword ptr [00000000031e9208]` is the actual call to `USER32!MessageBoxA`
* This means that prior to the above mentioned instruction, there must be references to the variables that are passed to the `MessageBoxA` function:

![](../../.gitbook/assets/reflective-dll-injection-mem-analysis.png)

If we inspect the `00000000031e103e` 0x30 bytes earlier, we can see some suspect memory addresses and the call instruction almost immediatley after that:

![](../../.gitbook/assets/reflective-dll-injection-variables.png)

Upon inspecting those two  addresses - those are indeed holding the values the `MessageBoxA` prints out upon successful DLL injection into the victim process:

```cpp
0:007> da 00000000`031e92c8
00000000`031e92c8  "Reflective Dll Injection"
0:007> da 00000000`031e92e8
00000000`031e92e8  "Hello from DllMain!"
```

![](../../.gitbook/assets/reflective-dll-injection-strings.png)

Looking at the output of the `!address` function and correlating it with the addresses the variables are stored at, it can be derived that the memory region allocated for the evil dll is located in the range `031e0000 - 031f7000`:

![](../../.gitbook/assets/reflective-dll-injection-range.png)

Indeed, if we look at the `031e0000`, we can see the executable header and the strings can be also found further into the binary:

![](../../.gitbook/assets/reflective-dll-strings.gif)

{% embed data="{\"url\":\"https://github.com/stephenfewer/ReflectiveDLLInjection\",\"type\":\"link\",\"title\":\"stephenfewer/ReflectiveDLLInjection\",\"description\":\"Reflective DLL injection is a library injection technique in which the concept of reflective programming is employed to perform the loading of a library from memory into a host process. - stephenfe...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/1172185?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

