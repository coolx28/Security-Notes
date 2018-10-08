# Phishing: XLM / Macro 4.0

This lab is based on the research performed by [Stan Hegt from Outflank](https://outflank.nl/blog/2018/10/06/old-school-evil-excel-4-0-macros-xlm/).

## Weaponization

A Microsoft Excel Spreadsheet can be weaponized by firstly inserting a new sheet of type "MS Execel 4.0 Macro":

![](../../.gitbook/assets/phishing-xlm-create-new.png)

We can then execute command by typing into the cells:

```text
=exec("c:\shell.cmd")
=halt()
```

Note how we need to rename the `A1` cell to `Auto_Open` if we want the Macros to fire off once the document is opened:

![](../../.gitbook/assets/phishing-xlm-auto-open.png)

## Execution

Opening the document and enabling Macros pops a reverse shell:

![](../../.gitbook/assets/phishing-xlm-shell-auto-open.gif)

## Observations

As usual, look for any suspicious children originating from under the Excel.exe:

![](../../.gitbook/assets/phishing-xlm-procexp.png)

{% embed data="{\"url\":\"https://outflank.nl/blog/2018/10/06/old-school-evil-excel-4-0-macros-xlm/\",\"type\":\"link\",\"title\":\"Old school: evil Excel 4.0 macros \(XLM\) \| Outflank Blog\",\"description\":\"<p>In this post, I will dive into Excel 4.0 macros \(also called XLM macros – not XML\) for offensive purposes. If you grew up in the Windows 95 age or later, just as I did, you might have never heard of this technology that was introduced as early as 1992. Virtually all malicious macro documents…</p>\",\"icon\":{\"type\":\"icon\",\"url\":\"https://outflank.nl/favicon.png\",\"aspectRatio\":0}}" %}

