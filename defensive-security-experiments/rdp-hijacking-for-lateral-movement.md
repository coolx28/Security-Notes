---
description: >-
  This lab explores a technique that allows a SYSTEM account to move laterally
  through the network without the need for credentials.
---

# RDP Hijacking for Lateral Movement

## Execution

It is possible by design to switch from one user's desktop session to another through the Task Manager \(one of the ways\).

Below shows that there are two users on the system and currently the administrator session is in place:

![](../.gitbook/assets/rdp-admin.png)

Let's swtich to the `spotless` session - this requires knowing the user's password, which for this exercise is known, so lets enter it:

![](../.gitbook/assets/rdp-login.png)

![](../.gitbook/assets/rdp-password.png)

We are now reconnected to the `spotless` session:

![](../.gitbook/assets/rdp-spotless.png)

Now this is where it gets interesting. It is possible to reconnect to a users session without knowing their password if you have `SYSTEM` level privileges on the system.   
Let's elevate to `SYSTEM` using psexec \(privilege escalation exploits, service creation or any other technique will also do\):

```text
psexec -s cmd
```

![](../.gitbook/assets/rdp-system.png)

Enumerate available sessions on the host with `query user`:

![](../.gitbook/assets/rdp-sessions.png)

Switch to the `spotless` session without getting requested for a password by using the native windows binary `tscon.exe`that enables users to connect to other desktop sessions by specifying which session ID \(`2` in this case for the `spotless` session\) should be connected to which session \(`console` in this case, where the active `administator` session originates from\):

```text
cmd /k tscon 2 /dest:console
```

![](../.gitbook/assets/rdp-hijack-no-password.png)

Immediately after that, we are presented with the desktop session for `spotless`:

![](../.gitbook/assets/rdp-spotless-with-system.png)

## Observations

Looking at the logs, `tscon.exe` being executed as a `SYSTEM` user is something you may want to investigate further to make sure there is nothing malicious happening:

![](../.gitbook/assets/rdp-logs%20%281%29.png)

Also, note how `event_data.LogonID` and event\_ids `4778` \(logon\) and `4779` \(logoff\) events can be used to figure out which desktop sessions got disconnected/reconnected:

![Administrator session disconnected](../.gitbook/assets/rdp-session-disconnect.png)

![Spotless session reconnected \(hijacked\)](../.gitbook/assets/rdp-session-reconnect.png)

Just reinforcing the above - note the usernames and logon session IDs:

![](../.gitbook/assets/rdp-logon-sessions.png)



{% embed data="{\"url\":\"http://blog.gentilkiwi.com/securite/vol-de-session-rdp\",\"type\":\"link\",\"title\":\"Vol de session RDP \| Blog de Gentil Kiwi\",\"icon\":{\"type\":\"icon\",\"url\":\"http://blog.gentilkiwi.com/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"http://www.korznikov.com/2017/03/0-day-or-feature-privilege-escalation.html\",\"type\":\"link\",\"title\":\"Passwordless RDP Session Hijacking Feature All Windows versions\",\"description\":\"0-day or feature? All windows privilege escalation / session hijacking.\",\"icon\":{\"type\":\"icon\",\"url\":\"http://www.korznikov.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://i.ytimg.com/vi/OZqTK\_yQbHk/0.jpg\",\"width\":480,\"height\":360,\"aspectRatio\":0.75}}" %}

{% embed data="{\"url\":\"https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4778\",\"type\":\"link\",\"title\":\"Windows Security Log Event ID 4778 - A session was reconnected to a Window Station\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.ultimatewindowssecurity.com/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon\",\"type\":\"link\",\"title\":\"tscon\",\"description\":\"Windows Commands topic for \*\*\*\* -\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}



