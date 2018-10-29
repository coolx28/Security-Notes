# Domain Compromise via Unrestricted Kerberos Delegation

{% hint style="warning" %}
WIP
{% endhint %}

![](../.gitbook/assets/screenshot-from-2018-10-29-23-01-49.png)

![](../.gitbook/assets/screenshot-from-2018-10-29-23-02-51.png)

Set IIS01 computer to unrestricted delegation:

![](../.gitbook/assets/screenshot-from-2018-10-29-22-50-27.png)

Finding computers on a domain that have unrestricted kerberos delegation property set:

```csharp
Get-ADComputer -Filter {TrustedForDelegation -eq $true -and primarygroupid -eq 515} -Properties trustedfordelegation,serviceprincipalname,description
```

![](../.gitbook/assets/screenshot-from-2018-10-29-23-08-06.png)





