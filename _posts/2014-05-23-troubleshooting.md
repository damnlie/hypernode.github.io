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
