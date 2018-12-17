# Less Known Privileged Accounts

Administrators, Domain Admins, Enterprise Admins are well known AD groups that pentesters and red teamers will aim for, but there are other accounts memberships that can be useful as well.

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

Similarly to Server Operators, we can access the DC01 file system if we belong to `Backup Operators`:

![](../../.gitbook/assets/screenshot-from-2018-12-17-17-42-47.png)





{% embed url="https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory" %}

{% embed url="https://adsecurity.org/?p=3658" %}

