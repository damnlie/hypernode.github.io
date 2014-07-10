---
layout: post
title:  "Configuring run codes/run types in Nginx"
date:   2014-07-10 14:10:58
categories: manual usage nginx
excerpt: "Instructions on how to configure run codes and run types in Nginx."
---

If you need the `MAGE_RUN_CODE` or `MAGE_RUN_TYPE` environment variables need to be set for different storefronts, depending on the value of the http
Host-header, you can add a map in the `http`-context. Create a file
called `http.magerunmaps` containing:

```nginx
map $http_host $storecode {
  hostnames;
  default default;
  *.storefront1.com store1storecode.com;
  www.storefront2.com store2storecode.com;
}

map $http_host $storetype {
  hostnames;
  default store;
  something.example.com othertype;
}
```

The values for `$storecode` and `$storetype` will be assigned to the environment variables `MAGE_RUN_CODE` and `MAGE_RUN_TYPE`.


