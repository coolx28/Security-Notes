# T1096: Alternate Data Streams

## Execution

Creating a benign file:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
echo "this is benign" > benign.txt
Get-ChildItem
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/ads-benign.png)

Hiding an `evil.txt` file inside the `benign.txt`

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
cmd '/c echo "this is evil" > benign.txt:evil.txt'
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/ads-evil.png)

Note how the evil.txt file is not visible - that is because it is in the alternate data stream. Opening the benign.txt shows no signs of evil.txt. However, the data from evil.txt can still be accessed from the file as shown below in the commandline - `type benign.txt:evil.txt`:

![](../.gitbook/assets/ads-evil-2.png)

Additionally, we can view the data in the notepad as well by issuing:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
notepad .\benign.txt:evil.txt
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/ads-evil3.png)

## Observations

![](../.gitbook/assets/ads-commandline.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1096\",\"type\":\"link\",\"title\":\"NTFS File Attributes - ATT&CK for Enterprise\"}" %}

