---
layout: post
title:  "Troubleshooting"
date:   2014-05-23 11:44:41
categories: troubleshooting
excerpt: "Troubleshoot various generic errors"
---

## Mail sent from Hypernode is not delivered

Did you configure the right "Return-Path" in Magento? 

Also, to prevent your mail ending up in spam folders, it might help to add this to your domains DNS:

```
domain.com TXT "v=spf1 include:spf.hypernode.com ?all"
```

## Lesti FPC doesn't work

Are you using the latest version? Check here: http://gordonlesti.com/lestifpc/

## Connections to external systems (ie. inventory) don't work anymore

Try simulating the connection from other systems (```telnet 
blabla.com 443```). If that works, than possibly your Hypernode IP 
is filtered by a remote firewall. You could request to have your 
Hypernode IP added, however we discourage this, as your IP might 
change as part of our recovery procedures (server failures, 
datacenter failover, etc). The best option is to get your IP block 
removed altogether, or to update the IP dynamically (depending on 
the remote API). 
