---
layout: post
title:  "Configuring nginx"
date:   2014-02-16 14:10:58
categories: manual usage nginx
excerpt: "Basic instructions on how to configure nginx on hypernode,
coming from .htaccess-files on Apache."
---
Nginx does not use .htaccess files like Apache does. This means that
configuration previously done in .htaccess-files now has to be done in a
different format, explained in the [nginx
documentation](http://nginx.org/en/docs/).

Custom configuration to nginx can be made by placing files in the
`nginx`-directory inside your home directory. Files which name start
with `server.` will be parsed in the server context of nginx, files that
start with `http.` will be parsed in the http context.

When configuration files in the nginx-directory cannot be parsed by
nginx, a new file is created named `nginx_error_output` containing the
error message from nginx. It is advised to remove this file before
fixing the issue, since this file will not automatically be removed and
it might become unclear if the changed files are then installed or the
issue persists.

Some typical scenarios are explained here.

## Blocking or allowing certain IP addresses

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

## Protecting a directory with a password

To restrict access to a location to users with a password, you can use
the same htpasswd file that is used with Apache. To restrict access to a
location, you can use the [auth
basic](http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html)
directive.

__NOTE: Make sure to add `include "/etc/nginx/php-handler.conf";` to the end of a location-block, or PHP scripts inside it will not be executed!__

For example, to restrict access to the preview-directory using users and
passwords specified in the file `/data/web/public/preview/.htpasswd`,
create a file called `server.basicauth` containing:

```nginx
location /preview/ {
  auth_basic "Restricted area";
  auth_basic_user_file /data/web/public/preview/.htpasswd;
  include "/etc/nginx/php-handler.conf";
}
```

## Rewriting or redirecting URLs

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

## Run codes/run types for Magento

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

## Setting the server name

Some Magento module licenses are tied to a specific server name (accessible in the environment variable SERVER_NAME). To configure which name to use, you can use the [server names](http://nginx.org/en/docs/http/server_names.html) module. To explicitly make the server name be mydomain.com, create a file called `server.servername` containing:

```nginx
server_name mydomain.com
```
