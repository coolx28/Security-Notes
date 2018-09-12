---
description: >-
  Unload sysmon driver which causes the system to stop recording sysmon event
  logs.
---

# Unloading Sysmon Driver

## Execution

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```text
fltMC.exe unload SysmonDrv
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/sysmon-cmd.png)

## Observations

Windows event logs suggesting `SysmonDrv` was unloaded successfully:

![](../.gitbook/assets/sysmon-unload-log1.png)

As well as processes requesting special privileges:

![](../.gitbook/assets/sysmon-unload-log2.png)

Note how in the last 35 minutes since the driver was unloaded, no further process creation events were recorded although I spawned new processes during that time:

![](../.gitbook/assets/sysmon-last-event.png)

Note how the system thinks that the sysmon is still running, which it is, but not doing anything useful:

![](../.gitbook/assets/sysmon-running.png)

{% embed data="{\"url\":\"https://twitter.com/Moti\_B/status/1019307375847723008\",\"type\":\"rich\",\"title\":\"Moti on Twitter\",\"description\":\"fltmc unload Sysmondrv - unload the device driver and disable almost all logging except from networking, elegantly bypassing registry audit traps. How to catch it? You need event id 1 on description = \\\"Filter Manager Control Program\\\" and event id 255. \#Sysmon— Moti \(@Moti\_B\) July 17, 2018\\n\\n\",\"icon\":{\"type\":\"icon\",\"url\":\"https://abs.twimg.com/icons/apple-touch-icon-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"embed\":{\"type\":\"app\",\"html\":\"<blockquote class=\\\"twitter-tweet\\\" data-dnt=\\\"true\\\" align=\\\"center\\\"><p lang=\\\"en\\\" dir=\\\"ltr\\\">fltmc unload Sysmondrv - unload the device driver and disable almost all logging except from networking, elegantly bypassing registry audit traps. How to catch it? You need event id 1 on description = &quot;Filter Manager Control Program&quot; and event id 255. <a href=\\\"https://twitter.com/hashtag/Sysmon?src=hash&amp;ref\_src=twsrc%5Etfw\\\">\#Sysmon</a></p>&mdash; Moti \(@Moti\_B\) <a href=\\\"https://twitter.com/Moti\_B/status/1019307375847723008?ref\_src=twsrc%5Etfw\\\">July 17, 2018</a></blockquote>\\n<script async src=\\\"https://platform.twitter.com/widgets.js\\\" charset=\\\"utf-8\\\"></script>\\n\",\"maxWidth\":550,\"aspectRatio\":null}}" %}

