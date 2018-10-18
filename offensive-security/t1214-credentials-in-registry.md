# T1214: Credentials in Registry

## Execution

Scanning registry hives for the value `password`:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
reg query HKLM /f password /t REG_SZ /s
# or
reg query HKCU /f password /t REG_SZ /s
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Observations

Monitoring for commandline arguments that include `req query` and `password` should raise suspicion and may warrant further investigation:

![](../.gitbook/assets/passwords-registry.png)

{% embed url="https://attack.mitre.org/wiki/Technique/T1214" %}

