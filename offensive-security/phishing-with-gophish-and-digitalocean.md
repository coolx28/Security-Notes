# Phishing with GoPhish and DigitalOcean

{% hint style="info" %}
WIP
{% endhint %}

![](../.gitbook/assets/screenshot-from-2019-01-08-23-11-32.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-56-12.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-51-21.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-50-47.png)

![](../.gitbook/assets/peek-2019-01-08-22-47.gif)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-45-34.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-41-09.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-40-21.png)

![](../.gitbook/assets/screenshot-from-2019-01-08-22-37-41.png)

```text
ssh -i digital-ocean-authorised-keys root@68.183.113.176 -L3333:localhost:3333 -N -f
mantvydas@~: ssh root@68.183.113.176 -i digital-ocean-authorised-keys 
The authenticity of host '68.183.113.176 (68.183.113.176)' can't be established.
ECDSA key fingerprint is SHA256:BZG7hR4+fvbdusyC45IvScP1X+vbuPh8GWDv5al/qCE.
ECDSA key fingerprint is MD5:b9:54:b4:3e:63:a2:6b:61:a6:7e:7e:d7:98:46:b4:ce.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '68.183.113.176' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.4.0-141-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@ubuntu-s-1vcpu-1gb-nyc1-01:~# 68.183.113.176
68.183.113.176: command not found
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# apt-get install postfix mailutils
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package mailutils is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'mailutils' has no installation candidate
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# apt-get install postfix
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following package was automatically installed and is no longer required:
  grub-pc-bin
Use 'apt autoremove' to remove it.
The following additional packages will be installed:
  ssl-cert
Suggested packages:
  procmail postfix-mysql postfix-pgsql postfix-ldap postfix-pcre sasl2-bin
  dovecot-common postfix-cdb mail-reader postfix-doc openssl-blacklist
The following NEW packages will be installed:
  postfix ssl-cert
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 1,169 kB of archives.
After this operation, 3,759 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://mirrors.digitalocean.com/ubuntu xenial/main amd64 ssl-cert all 1.0.37 [16.9 kB]
Get:2 http://mirrors.digitalocean.com/ubuntu xenial-updates/main amd64 postfix amd64 3.1.0-3ubuntu0.3 [1,152 kB]
Fetched 1,169 kB in 0s (20.1 MB/s)
Preconfiguring packages ...
Selecting previously unselected package ssl-cert.
(Reading database ... 54500 files and directories currently installed.)
Preparing to unpack .../ssl-cert_1.0.37_all.deb ...
Unpacking ssl-cert (1.0.37) ...
Selecting previously unselected package postfix.
Preparing to unpack .../postfix_3.1.0-3ubuntu0.3_amd64.deb ...
Unpacking postfix (3.1.0-3ubuntu0.3) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for libc-bin (2.23-0ubuntu10) ...
Processing triggers for ufw (0.35-0ubuntu2) ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
Setting up ssl-cert (1.0.37) ...
Setting up postfix (3.1.0-3ubuntu0.3) ...
Adding group `postfix' (GID 117) ...
Done.
Adding system user `postfix' (UID 112) ...
Adding new user `postfix' (UID 112) with group `postfix' ...
Not creating home directory `/var/spool/postfix'.
Creating /etc/postfix/dynamicmaps.cf
Adding group `postdrop' (GID 118) ...
Done.
setting myhostname: ubuntu-s-1vcpu-1gb-nyc1-01
setting alias maps
setting alias database
changing /etc/mailname to ired.team
setting myorigin
setting destinations: $myhostname, ired.team, ubuntu-s-1vcpu-1gb-nyc1-01, localhost.localdomain, localhost
setting relayhost: 
setting mynetworks: 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
setting mailbox_size_limit: 0
setting recipient_delimiter: +
setting inet_interfaces: all
setting inet_protocols: all
/etc/aliases does not exist, creating it.
WARNING: /etc/aliases exists, but does not have a root alias.

Postfix is now set up with a default configuration.  If you need to make 
changes, edit
/etc/postfix/main.cf (and others) as needed.  To view Postfix configuration
values, see postconf(1).

After modifying main.cf, be sure to run '/etc/init.d/postfix reload'.

Running newaliases
Processing triggers for libc-bin (2.23-0ubuntu10) ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for ufw (0.35-0ubuntu2) ...
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# cat /etc/mail
mailcap        mailcap.order  mailname       
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# cat /etc/mailname 
ired.team
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 16:d9:9a:0f:a8:ad brd ff:ff:ff:ff:ff:ff
    inet 68.183.113.176/20 brd 68.183.127.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 10.10.0.5/16 brd 10.10.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::14d9:9aff:fe0f:a8ad/64 scope link 
       valid_lft forever preferred_lft forever
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# ss -ltn
State      Recv-Q Send-Q Local Address:Port               Peer Address:Port              
LISTEN     0      128          *:22                       *:*                  
LISTEN     0      100          *:25                       *:*                  
LISTEN     0      128         :::22                      :::*                  
LISTEN     0      100         :::25                      :::*                  
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# cd /opt
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt# mkdir gophish
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt# cd gophish/
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# ls
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# wget https://github.com/gophish/gophish/releases/download/0.7.1/gophish-v0.7.1-linux-64bit.zip
--2019-01-08 21:21:01--  https://github.com/gophish/gophish/releases/download/0.7.1/gophish-v0.7.1-linux-64bit.zip
Resolving github.com (github.com)... 192.30.253.112, 192.30.253.113
Connecting to github.com (github.com)|192.30.253.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/14508450/3d4d4b00-b427-11e8-972c-cdc06ef53c5b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190108T212101Z&X-Amz-Expires=300&X-Amz-Signature=26624865f5a0b440fab9f0062790789de0fe05d4aa300ae6ad86fb800bb05e90&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dgophish-v0.7.1-linux-64bit.zip&response-content-type=application%2Foctet-stream [following]
--2019-01-08 21:21:01--  https://github-production-release-asset-2e65be.s3.amazonaws.com/14508450/3d4d4b00-b427-11e8-972c-cdc06ef53c5b?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20190108%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20190108T212101Z&X-Amz-Expires=300&X-Amz-Signature=26624865f5a0b440fab9f0062790789de0fe05d4aa300ae6ad86fb800bb05e90&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dgophish-v0.7.1-linux-64bit.zip&response-content-type=application%2Foctet-stream
Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.133.19
Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.133.19|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 28260687 (27M) [application/octet-stream]
Saving to: ‘gophish-v0.7.1-linux-64bit.zip’

gophish-v0.7.1-linu 100%[===================>]  26.95M  14.2MB/s    in 1.9s    

2019-01-08 21:21:03 (14.2 MB/s) - ‘gophish-v0.7.1-linux-64bit.zip’ saved [28260687/28260687]

root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# unzi^C
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# apt install unzip
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following package was automatically installed and is no longer required:
  grub-pc-bin
Use 'apt autoremove' to remove it.
Suggested packages:
  zip
The following NEW packages will be installed:
  unzip
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 158 kB of archives.
After this operation, 530 kB of additional disk space will be used.
Get:1 http://mirrors.digitalocean.com/ubuntu xenial/main amd64 unzip amd64 6.0-20ubuntu1 [158 kB]
Fetched 158 kB in 0s (6,307 kB/s)
Selecting previously unselected package unzip.
(Reading database ... 54700 files and directories currently installed.)
Preparing to unpack .../unzip_6.0-20ubuntu1_amd64.deb ...
Unpacking unzip (6.0-20ubuntu1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up unzip (6.0-20ubuntu1) ...
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# unzip gophish-v0.7.1-linux-64bit.zip gophish-v0.7.1-linux-64bit.zip ^C
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# unzip gophish-v0.7.1-linux-64bit.zip 
Archive:  gophish-v0.7.1-linux-64bit.zip

root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# chmod +x gophish
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# ls
config.json  gophish                         LICENSE    static     VERSION
db           gophish-v0.7.1-linux-64bit.zip  README.md  templates
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# nano config.json 
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# nano config.json 
root@ubuntu-s-1vcpu-1gb-nyc1-01:~# nano /etc/postfix/main.cf
root@ubuntu-s-1vcpu-1gb-nyc1-01:/opt/gophish# ./gophish 

```

## References

{% embed url="https://docs.getgophish.com/user-guide/building-your-first-campaign/creating-the-template" %}

{% embed url="http://www.postfix.org/BASIC\_CONFIGURATION\_README.html" %}

