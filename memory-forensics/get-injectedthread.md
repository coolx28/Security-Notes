---
description: Exploring injected threads with Get-InjectedThreads.ps1 and WinDBG
---

# Exploring Injected Threads

Scan all the running processes for any injected threads:

```csharp
$a = Get-InjectedThread; $a
```

Double checking the payload found in the injected thread:

```csharp
($a.Bytes | ForEach-Object tostring x2) -join "\x"
```

The below WinDBG commands show:

1. Getting a list of threads in the injected process that is being debugged
2. Switching debugging context to 6th thread
3. Inspecting `StartAddress`of the 7th thread
4. Converting `StartAddress` from decimal \(Get-InjectedThread `StartAddress` output\) to hex

```csharp
~
~6s
0:007> ~.
.  7  Id: be4.d38 Suspend: 1 Teb: 000007ff`fffac000 Unfrozen
      Start: 00000000`036f0000
      Priority: 0  Priority class: 32  Affinity: f

0:007> ?0x036f0000
Evaluate expression: 57606144 = 00000000`036f0000
```

{% embed data="{\"url\":\"https://posts.specterops.io/defenders-think-in-graphs-too-part-1-572524c71e91\",\"type\":\"link\",\"title\":\"Defenders Think in Graphs Too! Part 1\",\"description\":\"Introduction to Get-InjectedThread and Series Introduction\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn-images-1.medium.com/fit/c/304/304/1\*D-FDlfkqivRBQZoESrwtqw.png\",\"width\":152,\"height\":152,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn-images-1.medium.com/max/834/1\*G8\_Rb1FcBsz9cWdzubDvSA.png\",\"width\":417,\"height\":721,\"aspectRatio\":1.7290167865707433}}" %}

{% embed data="{\"url\":\"https://blog.xpnsec.com/undersanding-and-evading-get-injectedthread/\",\"type\":\"link\",\"title\":\"Understanding and Evading Get-InjectedThread\",\"description\":\"One of the many areas of this field that I really enjoy is the &quot;cat and mouse&quot; game played between RedTeam and BlueTeam, each forcing the other to up their game. Often we see some awesome tools being released to help defenders detect malware or shellcode execution, and\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blog.xpnsec.com/assets/favicon/android-icon-192x192.png?v=f42aa5d9f9\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.xpnsec.com/content/images/2018/04/bypass3-1.png\",\"width\":1718,\"height\":1306,\"aspectRatio\":0.760186263096624}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows/desktop/api/winnt/ns-winnt-\_memory\_basic\_information\",\"type\":\"link\",\"title\":\"\_MEMORY\_BASIC\_INFORMATION\",\"description\":\"Contains information about a range of pages in the virtual address space of a process.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

