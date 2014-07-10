---
layout: post
title:  "Setting the server name."
date:   2014-07-10 14:10:58
categories: manual usage nginx
excerpt: "Instructions on how to set a server name in Nginx."
---

Some Magento module licenses are tied to a specific server name (accessible in the environment variable SERVER_NAME). To configure which name to use, you can use the [server names](http://nginx.org/en/doc$

```nginx
server_name mydomain.com
```

