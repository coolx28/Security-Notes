# SMTP

```text
Delivered-To: xxx@gmail.com
Received: by 2002:a81:1157:0:0:0:0:0 with SMTP id 84-v6csp4998183ywr;
        Tue, 2 Oct 2018 11:51:25 -0700 (PDT)
X-Google-Smtp-Source: ACcGV61rWqp1E/Dq7h+145ZifuvUWkJHDxYGEQCJVFcfppuUXnnyGTTp5GN2SxN4xDhIDhdrPp97
X-Received: by 2002:a17:902:6689:: with SMTP id e9-v6mr18028257plk.115.1538506285065;
        Tue, 02 Oct 2018 11:51:25 -0700 (PDT)
ARC-Seal: i=1; a=rsa-sha256; t=1538506285; cv=none;
        d=google.com; s=arc-20160816;
        b=u8bei/9S+/jeDJRr9PjXtzNJ8gohm3ZQtZ3LcACWVeANU4NXhIP+UifRuDwKn1ZseU
         KFHoXxj6y50dOxMKejouK6Lt5/tUtlAiAuvVwFmN7fdfN2w0VyhKv6T+JQ8ctClfSdaM
         V/pVDanH+bUKxpgnvto2KDY2iOLoq1gKHFcV8SVzh9qklQpeNB4WbU8aPlcnwkXPw2bp
         EqLsOEL3yEMauoxu62xAAueQg50arIcTzBUnhgk7cIYS9GqXGqxNJuS409NjtpU71dUm
         JE348119qU01A2DcbsViMfbNpDIp56jM7eXsKrIuBW2xH/6X+yFKKX4aUW3xtCH/Oc9a
         9V5w==
ARC-Message-Signature: i=1; a=rsa-sha256; c=relaxed/relaxed; d=google.com; s=arc-20160816;
        h=from:date:message-id;
        bh=YlscECf2wWowOFI8AefnsRrT3iR8e3568ybpdHzjKvQ=;
        b=s1mpEbypbCkFPK2leGYJ+LGh9wa6e7rbY1ra7HlbxFz9FvOHB8yFMuNUD79003sdmr
         wORUr03R1U3NnbZWuVU0Uq1v2mXPCOVoDULuk18K7kyY7bCxrjUQ4PHJ+7oqpRVb1Q/J
         w2TXdaSSHcM5+eJZwakj8mijDXcWWj8uub0vfGULdFrV+GCCu42Bx+Xgb0AGgb0zAE6s
         rze729Dd8yohuwOuFDmZltYz8hN9AdjZkKYlC3ixGkAfuiMzdrvmUXEfRWdt5Qng0CAR
         y9siHNIlusIsnXpk8ws/BXvMr/Wjn919zLPJ41eY6A5v/RrJV1tLH9pFqR/osDcY8up0
         1XKQ==
ARC-Authentication-Results: i=1; mx.google.com;
       spf=neutral (google.com: 206.189.221.162 is neither permitted nor denied by best guess record for domain of root@mail.nodspot.com) smtp.mailfrom=root@mail.nodspot.com
Return-Path: <root@mail.nodspot.com>
Received: from ubuntu-s-1vcpu-1gb-sfo2-01 ([206.189.221.162])
        by mx.google.com with ESMTP id p61-v6si17358929plb.55.2018.10.02.11.51.24
        for <xxx@gmail.com>;
        Tue, 02 Oct 2018 11:51:24 -0700 (PDT)
Received-SPF: neutral (google.com: 206.189.221.162 is neither permitted nor denied by best guess record for domain of root@mail.nodspot.com) client-ip=206.189.221.162;
Authentication-Results: mx.google.com;
       spf=neutral (google.com: 206.189.221.162 is neither permitted nor denied by best guess record for domain of root@mail.nodspot.com) smtp.mailfrom=root@mail.nodspot.com
Received: by ubuntu-s-1vcpu-1gb-sfo2-01 (Postfix, from userid 0) id AC6D93F155; Tue,
  2 Oct 2018 18:51:24 +0000 (UTC)
Message-Id: <20181002185124.AC6D93F155@ubuntu-s-1vcpu-1gb-sfo2-01>
Date: Tue,
  2 Oct 2018 18:51:19 +0000 (UTC)
From: root <root@mail.nodspot.com>

yala
```

