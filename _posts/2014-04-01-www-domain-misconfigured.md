---
layout: post
title:  "www-domain misconfigured"
date:   2014-04-01 10:01:12
categories: manual usage dns
excerpt: "The DNS of the www-record of your domain seems to be misconfigured"
---

You've probably reached this page because the DNS of the domain you're trying to visit hasn't been set up correctly. The www-record of the domain you're trying to visit points to our wwwizer-service, which rewrites requests for domain.com to www.domain.com. In this case www.domain.com points to the wwwizer service, which means that we have a loop. www.domain.com should use a CNAME to point to the Hypernode service address of the application. See our [documentation for more information about DNS and Hypernode](configuring-dns.html).
