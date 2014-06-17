---
layout: post
title:  "Using SSL with Hypernode"
date:   Tue Jun 17 11:14:35 CEST 2014
categories: SSL SNI
excerpt: "Everything you need to know about (multidomain) SSL and SNI"
---

It is possible to use SSL certificates associated with Byte hosting
plans (like Presence) to your Hypernode. This way, your shop will become
securely accessible using HTTPS.

## Limitations for multiple domains

There are some limitations when having SSL certificates for multiple
domains on Hypernode. At Byte, each different domainname will be served
on a different IP address, so the server knows which certificate to
present to the browser. On Hypernode there is only one IP address
available per node so this way of differentiating won't work.

To allow for multiple certificates per site, Hypernode uses a system
called [Server Name Identification or
SNI](http://en.wikipedia.org/wiki/Server_Name_Indication). With SNI, the
browser uses an extension of SSL to ask the server for a specific
certificate. Unfortunately older browsers (most notably Internet Explorer on Windows
XP and Android 2.2) don't support SNI and might be presented the wrong
certificate.

## Configuring

To configure SSL, got to the _Hypernode Settings_ in the Byte [Service
Panel](https://service.byte.nl/). Click on _SSL Certificate_ and add all
the certificates you want to have available on the node.

You can mark one certificate as _default_, which means that it will be
served if the browser doesn't send an SNI header or if it accesses the
site with an unknown hostname. Note that the user will probably get a
certificate error in that case, because the hostname doens't match the
name on the certificate.
