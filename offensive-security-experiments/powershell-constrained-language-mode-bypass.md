---
description: Understanding ConstrainedLanguageMode
---

# Powershell Constrained Language Mode ByPass

Constrained Language Mode in short locks down the nice features of Powershell usually required for complex attacks to be carried out.

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

![](../.gitbook/assets/ps-constrained.png)

With `ConstrainedLanguage`, trying to download a file from remote machine, we get `Access Denied`:

![](../.gitbook/assets/ps-constrained-download-denied.png)

However, if you have access to the system and enough privileges to change environment variables, the lock can be lifted by removing the variable `__PSLockdownPolicy` and re-spawning another powershell instance.

### Powershell Downgrade

If you have the ability to downgrade to Powershell 2.0, this can allow you to bypass the `ConstrainedLanguage`mode. Note how `$ExecutionContext.SessionState.LanguageMode` keeps returning `ConstrainedLangue` in powershell instances that were not launched with `-version Powershell 2` until it does not:

![](../.gitbook/assets/ps-downgrade.png)

{% embed data="{\"url\":\"https://blogs.msdn.microsoft.com/powershell/2017/11/02/powershell-constrained-language-mode/\",\"type\":\"link\",\"title\":\"PowerShell Constrained Language Mode\",\"description\":\"Automating the world one-liner at a time…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://blogs.msdn.microsoft.com/powershell/wp-content/themes/cloud-platform/images/favicon-msdn.png\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://www.blackhillsinfosec.com/powershell-without-powershell-how-to-bypass-application-whitelisting-environment-restrictions-av/\",\"type\":\"link\",\"title\":\"Powershell Without Powershell - How To Bypass Application Whitelisting, Environment Restrictions & AV - Black Hills Information Security\",\"description\":\"Brian Fehrman \(With shout outs to: Kelsey Bellew, Beau Bullock\) // In a previous blog post, we talked about bypassing AV and Application Whitelisting by using a method developed by Casey Smith. In a recent engagement, we ran into an environment with even more restrictions in place. Not only did they have AV and Application Whitelisting, they were …\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.blackhillsinfosec.com/apple-touch-icon.png\",\"width\":180,\"height\":180,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://www.blackhillsinfosec.com/wp-content/uploads/2016/11/header-1024x379.png\",\"width\":1024,\"height\":379,\"aspectRatio\":0.3701171875}}" %}

{% embed data="{\"url\":\"https://adsecurity.org/?p=2604\",\"type\":\"link\",\"title\":\"Detecting Offensive PowerShell Attack Tools – Active Directory Security\"}" %}

{% embed data="{\"url\":\"https://pentestn00b.wordpress.com/2017/03/20/simple-bypass-for-powershell-constrained-language-mode/\",\"type\":\"link\",\"title\":\"Simple Bypass for PowerShell Constrained Language Mode\",\"description\":\"Edit – I just had this pointed out to me that on Friday 17th March Lee Holmes wrote about this very attack on his blog here –  This is a pure coincidence and I was not aware of this blo…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://secure.gravatar.com/blavatar/41defc30f1872942ae9b680998df57ea?s=32\",\"width\":16,\"height\":16,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://pentestn00b.files.wordpress.com/2017/03/windows-features.png\",\"width\":551,\"height\":631,\"aspectRatio\":1.1451905626134302}}" %}

