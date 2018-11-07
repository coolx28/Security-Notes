# Abusing Active Directory ACLs/ACEs

{% hint style="danger" %}
WIP
{% endhint %}

## Execution

```text
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.ActiveDirectoryRights -eq "GenericAll"}
```

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-17-14.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-19-43.png)

![](../../.gitbook/assets/screenshot-from-2018-11-07-20-23-18.png)

## References

{% embed url="https://wald0.com/?p=112" %}

