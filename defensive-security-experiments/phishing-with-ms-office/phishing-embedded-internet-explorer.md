---
description: Code execution with embedded Internet Explorer Object
---

# Phishing: Embedded Internet Explorer

In this phishing lab I am just playing around with the POCs researched, coded and described by Yorick Koster in his blog post [Click me if you can, Office social engineering with embedded objects](https://securify.nl/blog/SFY20180801/click-me-if-you-can_-office-social-engineering-with-embedded-objects.html)

## Execution

![](../../.gitbook/assets/phishing-iex-video.gif)

{% file src="../../.gitbook/assets/webbrowser.docx" caption="WebBrowser.docx" %}

{% file src="../../.gitbook/assets/poc.ps1" caption="phishing-iex-embedded.ps1" %}

## Observations

![](../../.gitbook/assets/phishing-iex-ancestry.png)

As with other phishing documents, we can unzip the .docx and do a simple hexdump/strings on the `oleObject1.bin` to look for any suspicious strings referring to some sort of file/code execution:

![](../../.gitbook/assets/phishing-iex-olebin.png)

The CLSID object that makes this technique work is a `Shell.Explorer.1` object, as seen here:

```csharp
Get-ChildItem 'registry::HKEY_CLASSES_ROOT\CLSID\{EAB22AC3-30C1-11CF-A7EB-0000C05BAE0B}'
```

![](../../.gitbook/assets/phishing-explorer-obj.png)

{% embed data="{\"url\":\"https://securify.nl/blog/SFY20180801/click-me-if-you-can\_-office-social-engineering-with-embedded-objects.html\",\"type\":\"link\",\"title\":\"Click me if you can, Office social engineering with embedded objects\",\"description\":\"Blog\",\"icon\":{\"type\":\"icon\",\"url\":\"https://securify.nl/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://www.securify.nl/blog/SFY20180801/click-me-if-you-can-card.png\",\"width\":1600,\"height\":838,\"aspectRatio\":0.52375}}" %}

  


