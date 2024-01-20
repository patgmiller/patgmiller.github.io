---
title: Redirect www to non-www
author: Patrick Miller
date: "2012-11-28T03:18:29Z"
draft: false
categories:
- Programming
tags:
- apache
- .htaccess
---
I personally prefer the non-www domain to the www.* sub domain which evolved from the traditional web beginnings. Here is a generic .htaccess redirect from www.domain.tld to domain.tld.

{{< highlight shell >}}
Options +FollowSymLinks
RewriteEngine on
RewriteCond %{HTTP_HOST} ^www.(.*)$ [NC]
RewriteRule ^(.*)$ https://%1/$1 [L,R=301]
{{< / highlight >}}
