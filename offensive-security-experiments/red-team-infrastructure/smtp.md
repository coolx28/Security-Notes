---
description: SMTP Redirector + Stripping Email Headers
---

# SMTP Forwarders / Relays

## Setting up Relay Mail Server

I am going to set up a mail server that will be later used as an SMTP relay server. First off, a new Ubuntu droplet was created in Digital Ocean. 

![](../../.gitbook/assets/smtp-relay-droplet.png)

Postfix MTA was installed on the droplet with:

```text
apt-get install postfix
```

During postfix installation, I set `nodspot.com` as the mail name. After the installation, this can be checked/changed here:

```text
root@ubuntu-s-1vcpu-1gb-sfo2-01:~# cat /etc/mailname
nodspot.com
```

## DNS Records

DNS records for nodspot.com has to be updated like so:

![](../../.gitbook/assets/smtp-relay-maila.png)

![](../../.gitbook/assets/smtp-relay-mx.png)

## Testing Mail Server

Once postfix is installed and DNS is configured, we can test if the mail server is running by:

```text
telnet mail.nodspot.com 25
```

If successfull, you should see something like this:

![](../../.gitbook/assets/smtp-relay-test-mail.png)

We can further test if the mail server works by trying to send an actual email like so:

```text
root@ubuntu-s-1vcpu-1gb-sfo2-01:~# sendmail mantvydo@gmail.com
yolo
,
.
```

Soon enough, the email was received:

![](../../.gitbook/assets/smtp-relay-first-email.png)

...with the following headers - all as expected:

```text
Delivered-To: mantvydo@gmail.com
Received: by 2002:a81:1157:0:0:0:0:0 with SMTP id 84-v6csp5026946ywr;
        Tue, 2 Oct 2018 12:22:38 -0700 (PDT)
X-Google-Smtp-Source: ACcGV62oH69fwYnfV1zg+o+jbTpjQIzIzASmjoIsXbbfvdevE0LlkY32jflNS/acOtNBXiwzxYxP
X-Received: by 2002:a62:6547:: with SMTP id z68-v6mr17716388pfb.20.1538508158395;
        Tue, 02 Oct 2018 12:22:38 -0700 (PDT)
ARC-Seal: i=1; a=rsa-sha256; t=1538508158; cv=none;
        d=google.com; s=arc-20160816;
        b=FpEgLAICLn66cI+DDvpIsStUrReQ8fArcreT7FyS8SYcFQXFiK44HDcxwVHXCA8Xxb
         fUl+3HcerQEznHZMttZ4pZIMbN18pJS08wzuZdOlhGKAA2JSTkxGd+1PhJwDe1SFTYZc
         NoARSHL9opemJKg5YqZNjSTDSTfk/QqaCbq7mQL9LAwCKzanGSNR/R/28WymYrdRACOR
         GSmDCVvPaUaoemIP8+GwXkfU5Gkk49+F7t9Jbg23HKKq/YOhwF3ryeOEVfn74bhtZIkM
         QcUzWn5WSL0lIm0nbd2t7677/wcabOg0TCoZj1IHg+I7yLXE7+QZOYX1TguKu16oZeqt
         mTIA==
ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed; d=google.com; s=arc-20160816;
        h=from:date:message-id;
        bh=VSFU9fKoMQMmtQzPFdmefDuA+phTpwZXd9k5xGRzwRs=;
        b=VZ2vHjhPUSs17PXAUDyjYzm0w5sdQYqFx7h9iirh/BF1krrl3MQg4QAgfeo0py9qZH
         Xf8/9HmNe1pIgxnZiiZJeVijXeSHCIB4XkG4HYFJY2m/gQ9oZ4JSMfX/Kiw/CXEmbt71
         YP5S7yQKQNkHw24XnP3WUeDDQ7XvENEfPIS+LlCVtQOPT8fM9TAWQReKz06idynolfhR
         7P73wH8igwPea7586wdhSOtDYCURSMKTNVb8yP2eEPNBlP2u2jUrFImG2D2/lke4O6Iu
         7zu96tCYEY9FVG11dPFheKlMjvMoL4rqPSAQ3zty4Cbi4Vy2Is6f/VF8AYZ34i0FJooj
         eEkw==
ARC-Authentication-Results: i=1; mx.google.com;
       spf=pass (google.com: domain of root@nodspot.com designates 206.189.221.162 as permitted sender) smtp.mailfrom=root@nodspot.com
Return-Path: <root@nodspot.com>
Received: from ubuntu-s-1vcpu-1gb-sfo2-01 ([206.189.221.162])
        by mx.google.com with ESMTP id 38-v6si3160283pgr.237.2018.10.02.12.22.38
        for <mantvydo@gmail.com>;
        Tue, 02 Oct 2018 12:22:38 -0700 (PDT)
Received-SPF: pass (google.com: domain of root@nodspot.com designates 206.189.221.162 as permitted sender) client-ip=206.189.221.162;
Authentication-Results: mx.google.com;
       spf=pass (google.com: domain of root@nodspot.com designates 206.189.221.162 as permitted sender) smtp.mailfrom=root@nodspot.com
Received: by ubuntu-s-1vcpu-1gb-sfo2-01 (Postfix, from userid 0) id DC6DD3F156; Tue,
  2 Oct 2018 19:22:37 +0000 (UTC)
Message-Id: <20181002192237.DC6DD3F156@ubuntu-s-1vcpu-1gb-sfo2-01>
Date: Tue,
  2 Oct 2018 19:22:31 +0000 (UTC)
From: root <root@nodspot.com>

yolo
,
```



{% code-tabs %}
{% code-tabs-item title="/etc/postfix/header\_checks" %}
```text
/^Received:.*/              IGNORE
/^X-Originating-IP:/    IGNORE
/^X-Mailer:/            IGNORE
/^Mime-Version:/        IGNORE
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% embed data="{\"url\":\"https://serverfault.com/questions/91954/how-do-i-remove-these-junk-mail-headers\",\"type\":\"link\",\"title\":\"How do I remove these junk mail headers?\",\"description\":\"I\'m using Postfix 2.3.3 and mail sent from my server always add useless headers which I\'d like to remove. Currently I\'m only using the PHP mail\(\) function to send mail. Return-Path:  Received: fro...\",\"icon\":{\"type\":\"icon\",\"url\":\"https://cdn.sstatic.net/Sites/serverfault/img/apple-touch-icon.png?v=6c3100d858bb\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://cdn.sstatic.net/Sites/serverfault/img/apple-touch-icon@2.png?v=9b1f48ae296b\",\"width\":316,\"height\":316,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://major.io/2013/04/14/remove-sensitive-information-from-email-headers-with-postfix/\",\"type\":\"link\",\"title\":\"Remove sensitive information from email headers with postfix - major.io\",\"description\":\"I’m in the process of moving back to a postfix/dovecot setup for hosting my own mail and I wanted a way to remove the more sensitive email headers that are normally generated when I send mail. My goal is to hide the originating IP address of my mail as well as my mail client type …\",\"icon\":{\"type\":\"icon\",\"url\":\"https://major.io/wp-content/themes/wintersong-pro/images/favicon.ico\",\"aspectRatio\":0}}" %}

{% embed data="{\"url\":\"https://www.youtube.com/watch?v=mRUGEygkDEQ\",\"type\":\"video\",\"title\":\"How to redirect your domain emails to gmail for FREE Digital Ocean, PostFix, DNS Setup\",\"description\":\"🔥 https://goo.gl/olppgw 🔥 ← Want to become a Full-Stack Developer? I\'ll show you how\\n\\nThe blog post → http://goo.gl/ULZ1pu\\n\\n★☆★FOLLOW ALONG★☆★\\nBlog     → http://goo.gl/eEuWa7a\\nTwitter  → http://goo.gl/oX8kJz\\nGoggle+  → http://goo.gl/bqXqp1\\nFacebook → http://goo.gl/aEZzPE\\n\\nCreating a digital empire is not easy, let’s say that their is 40 domain names that are under the umbrella of your consulting firm. No problem right? just jump unto google business and pay the $5 dollars per domain. Although this solution works, and it’s the most reliable option that I have found for the price.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://www.youtube.com/yts/img/favicon\_144-vfliLAfaB.png\",\"width\":144,\"height\":144,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://i.ytimg.com/vi/mRUGEygkDEQ/mqdefault.jpg\",\"width\":320,\"height\":180,\"aspectRatio\":0.5625},\"embed\":{\"type\":\"player\",\"url\":\"https://www.youtube.com/embed/mRUGEygkDEQ?rel=0&showinfo=0\",\"html\":\"<div style=\\\"left: 0; width: 100%; height: 0; position: relative; padding-bottom: 56.2493%;\\\"><iframe src=\\\"https://www.youtube.com/embed/mRUGEygkDEQ?rel=0&amp;showinfo=0\\\" style=\\\"border: 0; top: 0; left: 0; width: 100%; height: 100%; position: absolute;\\\" allowfullscreen scrolling=\\\"no\\\"></iframe></div>\",\"aspectRatio\":1.7778}}" %}

