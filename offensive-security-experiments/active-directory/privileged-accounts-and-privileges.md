# Privileged Accounts and Privileges

Administrators, Domain Admins, Enterprise Admins are well known AD groups that pentesters and red teamers will aim for, but there are other account memberships and access token privileges that can be useful during engagements.

## Account Operators

* Allows creating non administrator accounts and groups on the domain
* Allows logging in to the DC locally

Note the spotless' user membership:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-01-38.png)

However, we can still add new users:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-01-47.png)

As well as login to DC01 locally:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-05-35.png)

## Server Operators

This membership allows users to configure Domain Controllers with the following privileges:

* Allow log on locally
* Back up files and directories
* Change the system time
* Change the time zone
* Force shutdown from a remote system
* Restore files and directories
* Shut down the system

Note how we cannot access files on the DC with current membership:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-38-43.png)

However, if the user belongs to `Server Operators`:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-38-58.png)

The story changes:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-39-08.png)

## Backup Operators

As with `Server Operators` membership, we can access the `DC01` file system if we belong to `Backup Operators`:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-42-47.png)

## SeLoadDriverPrivilege

A very dangerous privilege to assign to any user - it allows the user to load kernel drivers and execute code with kernel privilges aka `NT\System`. See how `offense\spotless` user has this privilege:

![](../../.gitbook/assets/screenshot-from-2018-12-17-22-40-30.png)

`Whoami /priv` shows the privilege is disabled by default:

![](../../.gitbook/assets/screenshot-from-2018-12-17-21-59-15.png)

However, the below code allows enabling that privilege fairly easily:

{% code-tabs %}
{% code-tabs-item title="privileges.cpp" %}
```cpp
#include "stdafx.h"
#include <windows.h>
#include <stdio.h>

int main()
{
	TOKEN_PRIVILEGES tp;
	LUID luid;
	bool bEnablePrivilege(true);
	HANDLE hToken(NULL);
	OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, &hToken);

	if (!LookupPrivilegeValue(
		NULL,            // lookup privilege on local system
		L"SeLoadDriverPrivilege",   // privilege to lookup 
		&luid))        // receives LUID of privilege
	{
		printf("LookupPrivilegeValue error: %un", GetLastError());
		return FALSE;
	}
	tp.PrivilegeCount = 1;
	tp.Privileges[0].Luid = luid;
	
	if (bEnablePrivilege) {
		tp.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED;
	}
	
	// Enable the privilege or disable all privileges.
	if (!AdjustTokenPrivileges(
		hToken,
		FALSE,
		&tp,
		sizeof(TOKEN_PRIVILEGES),
		(PTOKEN_PRIVILEGES)NULL,
		(PDWORD)NULL))
	{
		printf("AdjustTokenPrivileges error: %x", GetLastError());
		return FALSE;
	}

	system("cmd");
    return 0;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

We compile and launch the application and the privilege is now enabled:

![](../../.gitbook/assets/screenshot-from-2018-12-17-22-45-54.png)

See below references for further instructions on the more detailed exploitation of this privilege.

## References

{% embed url="https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory" %}

{% embed url="https://docs.microsoft.com/en-us/windows/desktop/secauthz/enabling-and-disabling-privileges-in-c--" %}

{% embed url="https://adsecurity.org/?p=3658" %}

{% embed url="https://www.tarlogic.com/en/blog/abusing-seloaddriverprivilege-for-privilege-escalation/" %}

{% embed url="https://github.com/killswitch-GUI/HotLoad-Driver/blob/master/NtLoadDriver/EXE/NtLoadDriver-C%2B%2B/ntloaddriver.cpp\#L13" %}

{% embed url="https://github.com/tandasat/ExploitCapcom" %}

{% embed url="https://github.com/TarlogicSecurity/EoPLoadDriver/blob/master/eoploaddriver.cpp" %}

{% embed url="https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys" %}

