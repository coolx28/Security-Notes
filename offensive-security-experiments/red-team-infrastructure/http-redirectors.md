# HTTP Redirectors

```text
iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.2:80
iptables -t nat -A POSTROUTING -j MASQUERADE
iptables -I FORWARD -j ACCEPT
iptables -P FORWARD ACCEPT
sysctl net.ipv4.ip_forward=1
```

![](../../.gitbook/assets/redirectors-iptables.png)

{% embed data="{\"url\":\"https://github.com/bluscreenofjeff/Red-Team-Infrastructure-Wiki\#https\",\"type\":\"link\",\"title\":\"bluscreenofjeff/Red-Team-Infrastructure-Wiki\",\"description\":\"Wiki to collect Red Team infrastructure hardening resources - bluscreenofjeff/Red-Team-Infrastructure-Wiki\",\"icon\":{\"type\":\"icon\",\"url\":\"https://github.com/fluidicon.png\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://avatars1.githubusercontent.com/u/6732151?s=400&v=4\",\"width\":400,\"height\":400,\"aspectRatio\":1}}" %}

