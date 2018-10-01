---
description: 'Traffic forwarders using Linux iptables, socat'
---

# Redirectors / Forwarders

## Purpose

Re-directors or traffic forwarders are essentially proxies between the red teaming server \(say the one for sending phishing emails or a C2 server\) and the victim: `victim <> re-director <> team server`

The purpose of the re-director host is as usual:

* Obscure the red-teaming server by concealing its IP address. In other words - the victim will see traffic coming from the re-director host rather than the team server.
* If incident responders detect suspicious activity originating from the redirector, it can be "easily" decommissioned and replaced with another one, which is "easier" than rebuilding the team server.

## Simple HTTP Forwarding with iptables

Simple forwarder is just that - it simply listens on a given interface and port and forwards all the traffic it receives on that port, to the listener on the team server.

Environment in this example:

* Attacker host and a listening port: `10.0.0.2:80`
* Re-director host and a listening port: `10.0.0.5:80`
* Victim host: `10.0.0.11`

An easy way to create an HTTP re-director is to use a Linux box and its iptables capability. Below shows how to turn a Linux box into an HTTP re-director. In this case, all the HTTP traffic to `10.0.0.5:80` will be forwarded to `10.0.0.2:80` :

```text
iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.2:80
iptables -t nat -A POSTROUTING -j MASQUERADE
iptables -I FORWARD -j ACCEPT
iptables -P FORWARD ACCEPT
sysctl net.ipv4.ip_forward=1
```

Checking that the iptables rules were created successfully:

![](../../.gitbook/assets/redirectors-iptables.png)

{% embed data="{\"url\":\"https://github.com/bluscreenofjeff/Red-Team-Infrastructure-Wiki\#https\",\"type\":\"link\",\"title\":\"bluscreenofjeff/Red-Team-Infrastructure-Wiki\",\"description\":\"Wiki to collect Red Team infrastructure hardening resources - bluscreenofjeff/Red-Team-Infrastructure-Wiki\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/6732151?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://www.frozentux.net/iptables-tutorial/chunkyhtml/x4033.html\",\"type\":\"link\",\"title\":\"DNAT target\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.frozentux.net/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"http://linux-training.be/networking/ch14.html\",\"type\":\"link\",\"title\":\"Chapter 14. iptables firewall\"}" %}

[https://www.thegeekstuff.com/2011/01/iptables-fundamentals/](https://www.thegeekstuff.com/2011/01/iptables-fundamentals/)

