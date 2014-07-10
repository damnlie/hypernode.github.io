---
layout: post
title:  "Rewriting/directing URLs."
date:   2014-07-10 14:10:58
categories: manual usage nginx
excerpt: "Instructions on how to rewrite/redirect URLs in Nginx."
---

The default Magento rewrite rules are already preconfigured for you. The
[rewrite
module](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html) of
nginx has a different syntax, but is generally easier. To rewrite all
URL's in the format of `/invoice/345232.pdf` to `/invoice.php?id=345232`
you would create a file called `server.rewrites` containing:

```nginx
rewrite ^/invoice/(\d+).pdf$ /invoice.php?id=$1 break;
```

If you want to redirect instead of rewrite, you can specify the rewrite
target as a full URL starting with `http://` or `https://`, or you can
add the `redirect`-flag to the end of the rule for a 302 temporary
redirect, or `permanent` for a 301 permanent redirect.

