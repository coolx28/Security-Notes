# Powershell without Powershell \(?\)

## Powershell Inside Powershell

For fun - creating another powershell instance inside powershell without actually spawning a new `powershell.exe` process:

![](../.gitbook/assets/ps-invoke.gif)

## Constrained Language Mode

Enabling constrained language mode, that does not allow powershell execute complex attacks \(i.e. mimikatz\):

```csharp
[Environment]::SetEnvironmentVariable(‘__PSLockdownPolicy‘, ‘4’, ‘Machine‘)
```

Checking constrained language mode is enabled:

```csharp
PS C:\Users\mantvydas> $ExecutionContext.SessionState.LanguageMode
ConstrainedLanguage
```



{% embed data="{\"url\":\"https://www.blackhillsinfosec.com/powershell-without-powershell-how-to-bypass-application-whitelisting-environment-restrictions-av/\",\"type\":\"link\",\"title\":\"Powershell Without Powershell - How To Bypass Application Whitelisting, Environment Restrictions & AV - Black Hills Information Security\",\"description\":\"Brian Fehrman \(With shout outs to: Kelsey Bellew, Beau Bullock\) // In a previous blog post, we talked about bypassing AV and Application Whitelisting by using a method developed by Casey Smith. In a recent engagement, we ran into an environment with even more restrictions in place. Not only did they have AV and Application Whitelisting, they were …\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.blackhillsinfosec.com/apple-touch-icon.png\",\"width\":180,\"height\":180,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://www.blackhillsinfosec.com/wp-content/uploads/2016/11/header-1024x379.png\",\"width\":1024,\"height\":379,\"aspectRatio\":0.3701171875}}" %}

{% embed data="{\"url\":\"https://adsecurity.org/?p=2604\",\"type\":\"link\",\"title\":\"Detecting Offensive PowerShell Attack Tools – Active Directory Security\"}" %}

