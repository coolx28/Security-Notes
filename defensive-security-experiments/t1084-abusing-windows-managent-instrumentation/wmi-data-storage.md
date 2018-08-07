# WMI Data Storage

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

![](../../.gitbook/assets/wmi-data-storage-newclass.png)

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

