---
description: WIP
---

# Masquerading Processes in Userland through \_PEB

```text
dt _peb @$peb
 +0x020 ProcessParameters : 0x00000000`005e1f60 _RTL_USER_PROCESS_PARAMETERS
dt _RTL_USER_PROCESS_PARAMETERS 0x00000000`005e1f
dt _UNICODE_STRING 0x00000000`005e1f60+60
0:002> du 0x00000000`005e280e
00000000`005e280e  "C:\tools\nc.exe"

eu 0x00000000`005e280e "C:\\Windows\\System32\\notepad.exe"

//shows truncated string

//add string terminator
eb 0x00000000`005e280e+3d 0x0

//check - should show notepad.exe now
du 0x00000000`005e280e

//increase buffer lenght
eb 0x00000000`005e1f60+60 3e

//full string now
dt _UNICODE_STRING 0x00000000`005e1f60+60

//remove commandline params
eb 0x00000000`005e1f60+70 0x0
0:002> dt _UNICODE_STRING 0x00000000`005e1f60+70


```

![](../.gitbook/assets/masquerade-1.png)

![](../.gitbook/assets/masquerade-2.png)

![](../.gitbook/assets/masquerade-3.png)

![](../.gitbook/assets/masquerade-4.png)

![](../.gitbook/assets/masquerade-5.png)

![](../.gitbook/assets/masquerade-6.png)

![](../.gitbook/assets/masquerade-7.png)

![](../.gitbook/assets/masquerade-8.png)

![](../.gitbook/assets/masquerade-9.png)

![](../.gitbook/assets/masquerade-10.png)

![](../.gitbook/assets/masquerade-11.png)

![](../.gitbook/assets/masquerade-12.png)

![](../.gitbook/assets/masquerade-13.png)

![](../.gitbook/assets/masquerade-14.png)





