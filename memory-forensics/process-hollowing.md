# Exploring Process Environment Block \(PEB\)

This lab explores the some of the things that can be found by the PEB in a process memory.

## Basics

First of, checking what members the `_PEB` structure actually entails:

```text
dt _peb
```

There are many fields in the structure among which there are ImageBaseAddresss and ProcessParameters which are interesting fields:

![](../.gitbook/assets/peb-structure%20%281%29.png)

Getting the PEB address of the process:

```bash
0:001> r $peb
$peb=000007fffffd5000
```

The `_PEB` structure can now be overlaid on the memory pointed to by the `$peb` to see what values the structure members are holding/pointing to:

```bash
0:001> dt _peb @$peb
```

\_PEB structure is now populated with data:

![](../.gitbook/assets/peb-overlay.png)

Let's check what's in memory at address `0000000049d40000` - pointed by the ImageBaseAddress member of the \_peb structure:

```cpp
0:001> db 0000000049d40000 L100
```

Hey, this is the actual binary image of the running process:

![](../.gitbook/assets/peb-baseimage.png)

Another way of finding the `ImageBaseAddress` is:

```csharp
0:001> dt _peb
ntdll!_PEB
//snip
      +0x010 ImageBaseAddress : Ptr64 Void
//snip

0:001> dd @$peb+0x010 L2
000007ff`fffd5010  49d40000 00000000

// 49d40000 00000000 is little-endian byte format - need to invert
0:001> db 0000000049d40000 L100
```

Let's find the commandline the process was started with:

```cpp
dt _peb @$peb processp*
ntdll!_PEB
   +0x020 ProcessParameters : 0x00000000`002a1f40 _RTL_USER_PROCESS_PARAMETERS

dt _RTL_USER_PROCESS_PARAMETERS 0x00000000`002a1f40
```

![](../.gitbook/assets/peb-cmdline.png)

We can be more direct and ask the same question like so:

```cpp
0:001> dt _UNICODE_STRING 0x00000000`002a1f40+70
ntdll!_UNICODE_STRING
 ""C:\Windows\system32\cmd.exe" "
   +0x000 Length           : 0x3c
   +0x002 MaximumLength    : 0x3e
   +0x008 Buffer           : 0x00000000`002a283c  ""C:\Windows\system32\cmd.exe" "
```

or even this:

```cpp
0:001> dd 0x00000000`002a1f40+70+8 L2
00000000`002a1fb8  002a283c 00000000
0:001> du 00000000002a283c
00000000`002a283c  ""C:\Windows\system32\cmd.exe" "
```

![](../.gitbook/assets/peb-cmdline2.png)

## Convenience

We can forget about all of the above and just use:

```text
!peb
```

To get a nicely formatted key PEB information:

![](../.gitbook/assets/peb.png)

```bash
dt _PEB @$peb
// or

s -u 00000000`003d1378 L100000 "text"
```



