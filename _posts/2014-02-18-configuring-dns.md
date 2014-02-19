---
layout: post
title:  "DNS and hypernode"
date:   2014-02-18 17:12:03
categories: manual usage dns
excerpt: "Instructions on how to configure DNS for hypernode"
---

Hypernode is a cloud-based hosting service. This means that the IP
address of nodes might change unexpectedly.

If your DNS is hosted by Byte, we will automatically configure DNS for
you we'll make sure that your domain names always point to the right IP
address. You won't have to read the rest of this document because you're
good to go.

If you choose to host DNS at a third party provider, there are some
configuration changes you will need to make to point your domain name to
hypernode.

To summarize the possible solutions, explained in more detail below:

* Have your DNS hosted at Byte
* Have a CNAME for your www-domain + an ANAME/ALIAS pseudo-record for
  your naked domain
* Have a CNAME for your www-domain + an A record for your naked domain
  pointing to a redirector that redirects to the www-domain

## CNAME to hypernode

These examples assume you have a site called _yourdomain.com_ and a
hypernode called _yourd01.hypernode.io_.

The recommended way to configure your DNS is to create a CNAME record in
your DNS configuration, pointing to hypernode:

```
www.yourdomain.com      CNAME       yourd01.hypernode.io
```

This means that _www.yourdomain.com_ will point to wherever
_yourd01.hypernode.io_ points, and we will make sure that that always
points to the correct IP address.

## 'Apex' or 'naked' domain names

Problems arise when people visit your _naked_ (also called _apex_)
domainname: _yourdomain.com_, without the 'www.'-prefix. It is not
possible to create a CNAME record from your apex domain.

Some DNS providers, like [DNSimple](https://dnsimple.com/) or [DNS Made
Easy](http://www.dnsmadeeasy.com/) have the possibility to create ALIAS
or ANAME pseudo-records. These behave like CNAME-records for apex
domains. Unfortunately they are not wide-spread yet, so the chances are your provider doesn't support it.

Another solution is to redirect traffic for the apex domain
_yourdomain.com_ to its www-counterpart _www.yourdomain.com_. You might be
able to configure redirecting with your DNS provider, or you can use the
free service from [wwwizer](http://wwwizer.com/naked-domain-redirect).
Just configure the following A record for your apex domain and it will
redirect all traffic for you.

```
yourdomain.com          A           174.129.25.170
```

Note that the redirect server doesn't have your SSL certificate
configured, so people that directly visit https://yourdomain.com/, will
get a certificate warning from the browser.

