---
description: Defense Evasion
---

# T1130: Install Root Certificate

## Execution

Adding a certificate with a native windows binary:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
certutil.exe -addstore -f -user Root C:\Users\spot\Downloads\certnew.cer
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/certs-certutil.png)

Checking to see the certificate got installed:

![](../.gitbook/assets/certs-installed.png)

Adding a certificate with powershell:

{% code-tabs %}
{% code-tabs-item title="attacker@victim" %}
```csharp
Import-Certificate -FilePath C:\Users\spot\Downloads\certnew.cer -CertStoreLocation Cert:\CurrentUser\Root\
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/certs-add-with-ps.png)

## Observations

Advanced poweshell logging to the rescue:

![](../.gitbook/assets/certs-ps-logging.png)

Commandline logging:

![](../.gitbook/assets/certs-logs.png)

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1130\",\"type\":\"link\",\"title\":\"Install Root Certificate - ATT&CK for Enterprise\"}" %}



