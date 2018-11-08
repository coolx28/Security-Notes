# Abusing Active Directory ACLs/ACEs

{% hint style="danger" %}
WIP
{% endhint %}

## Execution

### GenericAll on User

```text
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.ActiveDirectoryRights -eq "GenericAll"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-17-14.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-19-43.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-23-18.png)

### GenericAll on Group

```text
Get-NetGroup "domain admins" -FullData
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-09-50-20.png)

```text
 Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-08-09-52-10.png)

![](../../.gitbook/assets/peek-2018-11-08-10-07.gif)

## References

{% embed url="https://wald0.com/?p=112" %}

