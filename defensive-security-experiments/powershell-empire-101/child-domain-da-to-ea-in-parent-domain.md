---
description: Explore Parent-Child Domain Trust Relationships and Privilege Escalation
---

# Child Domain DA to EA in Parent Domain

This lab is based on an [Empire Case Study](https://enigma0x3.net/2016/01/28/an-empire-case-study/) and its goal is to get more familiar with some of the concepts of Powershell Empire and its modules as well as Active Directory concepts such as Forests, Parent/Child domains and Trust Relationships and how they can be abused to escalate privileges.

## Domain Trust Relationships

After installing a child domain `red.offense.local` of a parent domain `offense.local`, Active Directory Domains and Trusts show the parent-child relationship between the domains as well as their default trusts:

![](../../.gitbook/assets/domains-trusts1.png)

Trusts between the two domains could be checked from powershell by issuing:

```csharp
Get-ADTrust -Filter *
```

The first console shows the domain trust relationship from `offense.local` perspective and the second one from `red.offense.local`. Note the the direction is `BiDirectional` which means that members can authenticate from one domain to another when they want to access shared resources:

![](../../.gitbook/assets/domains-trusts2.png)

{% embed data="{\"url\":\"https://enigma0x3.net/2016/01/28/an-empire-case-study/\",\"type\":\"link\",\"title\":\"An Empire Case Study\",\"description\":\"This post is part of the ‘Empire Series’, with some background and an ongoing list of series posts \[kept here\].  Empire has gotten a lot of use since its initial release at BSides Las Vegas. Most o…\",\"icon\":{\"type\":\"icon\",\"url\":\"https://s1.wp.com/i/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://enigma0x3.files.wordpress.com/2016/01/lab\_local\_krbtgt.png\",\"width\":1244,\"height\":1160,\"aspectRatio\":0.932475884244373}}" %}

{% embed data="{\"url\":\"http://www.harmj0y.net/blog/redteaming/trusts-you-might-have-missed/\",\"type\":\"link\",\"title\":\"Trusts You Might Have Missed\",\"description\":\"\[Edit 8/13/15\] – Here is how the old version 1.9 cmdlets in this post translate to PowerView 2.0: Get-NetForestTrusts  ->  Get-NetForestTrusts Get-NetForestDomains  ->  Get-NetForestDom…\",\"icon\":{\"type\":\"icon\",\"url\":\"http://www.harmj0y.net/blog/wp-content/uploads/2017/05/cropped-specter-192x192.png\",\"width\":192,\"height\":192,\"aspectRatio\":1},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"http://www.harmj0y.net/blog/wp-content/uploads/2014/06/powerview\_forest\_domain.png\",\"width\":735,\"height\":691,\"aspectRatio\":0.9401360544217687}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731404\(v%3dws.10\)\",\"type\":\"link\",\"title\":\"Understanding Trust Direction\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

{% embed data="{\"url\":\"https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-adtrust?view=winserver2012-ps\",\"type\":\"link\",\"title\":\"Get-ADTrust \(activedirectory\)\",\"description\":\"The Get-ADTrust cmdlet returns all trusted domain objects in the directory.\",\"icon\":{\"type\":\"icon\",\"url\":\"https://docs.microsoft.com/favicon.ico\",\"aspectRatio\":0},\"thumbnail\":{\"type\":\"thumbnail\",\"url\":\"https://docs.microsoft.com/\_themes/docs.theme/master/en-us/\_themes/images/microsoft-header.png\",\"width\":128,\"height\":128,\"aspectRatio\":1}}" %}

