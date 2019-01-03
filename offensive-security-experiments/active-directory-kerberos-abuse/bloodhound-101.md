# BloodHound with Kali Linux: 101

{% hint style="info" %}
WIP
{% endhint %}

```text
apt-get install bloodhound
neo4j console
bloodhound
```

```csharp
https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Ingestors/SharpHound.ps1
PS C:\tools> . .\SharpHound.ps1
PS C:\tools> Invoke-BloodHound
```

```csharp
runas /user:spotless@offense powershell
Invoke-BloodHound -CollectionMethod All -JSONFolder "c:\experiments\bloodhound"
```

![](../../.gitbook/assets/screenshot-from-2019-01-02-23-16-33.png)

![](../../.gitbook/assets/screenshot-from-2019-01-02-23-51-08.png)

![](../../.gitbook/assets/screenshot-from-2019-01-02-23-47-56.png)

![](../../.gitbook/assets/screenshot-from-2019-01-02-23-55-41.png)

![](../../.gitbook/assets/screenshot-from-2019-01-02-23-56-35.png)



{% page-ref page="abusing-active-directory-acls-aces.md" %}

{% embed url="https://github.com/BloodHoundAD/BloodHound/wiki" %}

