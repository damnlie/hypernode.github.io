---
layout: post
title:  "Blocking or allowing certain IP addresses"
date:   2014-07-10 14:10:58
categories: manual usage nginx
excerpt: "Instructions on how to block/allow certain IP addresses"
---

Blocking addresses is done using the [access
module](http://nginx.org/en/docs/http/ngx_http_access_module.html).

### Denying everyone across the site

To deny all access from certain addresses, create a file in the
`nginx`-directory in your homedir named `server.blacklist`, with the
following contents:

```nginx
deny 1.2.3.4;     # To deny a single server
deny 5.6.7.0/24;  # To deny a complete network
```

### Denying everyone across the site, except for certain addresses

To deny all access, except certain addresses, add a file named `server.whitelist`,
with the following contents:

```nginx
allow 1.2.3.4;   # Allow a single remote host
deny all;        # Deny everyone else
```


### Denying or allowing only a specific location

To deny access to everybody except certain addresses to a specific
directory, create a file called `server.private-dir` containing:

```nginx
location /private/ {
  allow 100.23.45.14;  ## Your specific IP
  deny all;
}
```
