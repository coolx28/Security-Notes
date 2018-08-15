# T1096: Alternate Data Streams

## Execution

Creating a benign text file:

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

Note how the evil.txt file is not visible through the explorer - that is because it is in the alternate data stream now. Opening the benign.txt shows no signs of evil.txt. However, the data from evil.txt can still be accessed as shown below in the commandline - `type benign.txt:evil.txt`:

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

Note that powershell can also help finding alternate data streams:

```csharp
Get-Item c:\experiment\evil.txt -Stream *
Get-Content .\benign.txt -Stream evil.txt
```

![](../.gitbook/assets/ads-powershell.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1096\",\"type\":\"link\",\"title\":\"NTFS File Attributes - ATT&CK for Enterprise\"}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/providers/filesystem-provider/get-item-for-filesystem?view=powershell-6\",\"type\":\"link\",\"title\":\"Get-Item for FileSystem\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://blog.malwarebytes.com/101/2015/07/introduction-to-alternate-data-streams/\",\"type\":\"link\",\"title\":\"Introduction to Alternate Data Streams - Malwarebytes Labs\",\"description\":\"What is an alternate data stream \(ADS\)? How it can be created and read, and how one can remove unwanted ADS?\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.malwarebytes.com/apple-touch-icon-152x152.png\",\"width\":152,\"height\":152,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://blog.malwarebytes.com/wp-content/uploads/2015/07/header1.png\",\"width\":992,\"height\":355,\"aspectRatio\":0.35786290322580644}}" %}



