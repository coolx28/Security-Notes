---
description: PowerShell remoting for lateral movement.
---

# T1028: WinRM

## Execution

Attacker establishing a ps-remoting session from a compromised system `10.0.0.2` connecting to a DC \(dc-mantvydas\) at `10.0.0.6`:

{% code-tabs %}
{% code-tabs-item title="attacker@10.0.0.2" %}
```bash
New-PSSession -ComputerName dc-mantvydas -Credential (Get-Credential)

  Id Name            ComputerName    ComputerType    State         ConfigurationName     Availability
 -- ----            ------------    ------------    -----         -----------------     ------------
  1 Session1        dc-mantvydas    RemoteMachine   Opened        Microsoft.PowerShell     Available

PS C:\Users\mantvydas> Enter-PSSession 1
[dc-mantvydas]: PS C:\Users\spotless\Documents> calc.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

Note the process ancestry \(run on Windows Server 2012\):

![](../.gitbook/assets/wsmprovhost-calc.png)

![](../.gitbook/assets/wsmprovhost-calc-sysmon.png)

On the host which initiated the connection, a `4648` logon attempt is logged showing what process initiated it, the hostname where it connected to and which account was used:

![](../.gitbook/assets/winrm-local-logon-events.png)

The below graphic shows that the logon events `4648` annd `4624` are being logged on both the hostname that initiated the connection \(`pc-mantvydas - 4648`\) and the hostname that was connected to \(`dc-mantvydas - 4624`\):

![](../.gitbook/assets/winrm-logons-both.png)

Additionally, `%SystemRoot%\System32\Winevt\Logs\Microsoft-Windows-WinRM%4Operational.evtx` on the host that initiated connection to the remote host, logs some interesting data for a task `WSMan Session initialize` :

```markup
- <Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
- <System>
  <Provider Name="Microsoft-Windows-WinRM" Guid="{A7975C8F-AC13-49F1-87DA-5A984A4AB417}" /> 
  <EventID>6</EventID> 
  <Version>0</Version> 
  <Level>4</Level> 
  <Task>3</Task> 
  <Opcode>1</Opcode> 
  <Keywords>0x4000000000000002</Keywords> 

  # connection iniation time
  <TimeCreated SystemTime="2018-07-25T21:13:36.511895800Z" /> 
  <EventRecordID>673</EventRecordID> 

  # a unique connection ID
  <Correlation ActivityID="{037F878B-8DF6-4F1A-BA51-432C3CDDCB47}" /> 

  # process ID that initiated the connection
  <Execution ProcessID="3172" ThreadID="2844" /> 
  <Channel>Microsoft-Windows-WinRM/Operational</Channel> 
  <Computer>PC-MANTVYDAS.offense.local</Computer> 
  <Security UserID="S-1-5-21-1731862936-2585581443-184968265-1001" /> 
  </System>
- <EventData>

  # remote host the connection was initiated to
  <Data Name="connection">dc-mantvydas/wsman?PSVersion=5.1.14409.1005</Data> 
  </EventData>
  </Event>
```

...same as above just in the actual screenshot:

![](../.gitbook/assets/winrm-eventlogs.png)

![](../.gitbook/assets/winrm-session-information.png)

Since we entered into a PS Shell on the remote system `(Enter-PSSession)` , there is another interesting log showing establishment of a remote shell - note that the ShellID corresponds to the earlier observed `Correlation ActivityID`:

![](../.gitbook/assets/winrm-shell.png)

{% embed data="{\"url\":\"http://www.hurryupandwait.io/blog/a-look-under-the-hood-at-powershell-remoting-through-a-ruby-cross-plaform-lens\",\"type\":\"link\",\"title\":\"A look under the hood at Powershell Remoting through a cross plaform lens\",\"description\":\"Many Powershell enthusiasts don\'t realize that when they are using commands like  New-PsSession  and streaming pipelines to a powershell runspace on a remote machine, they are actually writing a binary message wrapped in a SOAP envelope that leverages a protocol with the namesake of Windows Vista. N\",\"icon\":{\"type\":\"icon\",\"url\":\"https://static1.squarespace.com/static/53eb0624e4b022b28bd6cc45/t/53f1b170e4b086379f989c6f/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"http://static1.squarespace.com/static/53eb0624e4b022b28bd6cc45/t/575c93c6746fb9ca8e944664/1465685011839/?format=1000w\",\"width\":400,\"height\":415,\"aspectRatio\":1.0375}}" %}

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1028\",\"type\":\"link\",\"title\":\"Windows Remote Management - ATT&CK for Enterprise\"}" %}

