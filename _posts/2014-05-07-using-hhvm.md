---
layout: post
title:  "Using HHVM/HipHop"
date:   2014-05-07 11:44:41
categories: hhvm, php
excerpt: "Using HHVM on your Hypernode, combined with php-fpm"
---

Hypernode is one of the first professional platforms in the world to offer support for [HHVM](http://hhvm.com/); the experimental, blazingly fast PHP runtime developed by Facebook.

On your Hypernode you can use both traditional PHP-FPM and HHVM together. You may choose to run your entire site with PHP-FPM, HHVM, or mix and match both for individual parts of the site.

In your homedirectory on your Hypernode you find a directory `nginx`, which contains some sample configurations to change the behaviour of the webserver. In `handler.conf` (or any file ending with `.conf`) you may change the way php-files are handled.


## Enable HHVM

__NB: Every time you make a change to a file in `/data/web/nginx/`, our system will try to reload the webserver with the new configuration. If a parse error occurs and the webserver would not be able to reload, the reload is aborted. You may examine `/data/web/nginx/nginx_error_output` for errors.__

Our basic configuration dictates that each file ending with `.php` will be parsed by PHP-FPM. This is a safe default. In `handler.conf` you may specify that all files ending with `.php` should be handled by HHVM:

```
include /etc/nginx/hhvm-handler.conf;
```

If you only want certain parts of your site to be handled by HHVM, you could use nginx' [location](http://nginx.org/en/docs/http/ngx_http_core_module.html#location) directive:

```
location /index.php/apparel/shoes.html {
  include /etc/nginx/hhvm-handler.conf;
}

location /admin/ {
  include /etc/nginx/hhvm-handler.conf;
}
```

You don't need to specify a handler for other pages, these will be handled by our default, php-fpm.

You could also turn this around: run the entire site in HHVM, but exclude some parts:

```
location /checkout/ {
  include /etc/nginx/php-handler.conf;
}

location /blog/ {
  include /etc/nginx/php-handler.conf;
}

include /etc/nginx/hhvm-handler.conf;
```

__NB: nginx has a complex but [well documented](http://nginx.org/en/docs/http/ngx_http_core_module.html#location) order in which `location` directives are checked.__


## Troubleshooting
* If your change doesn't seem to work, first check that there are no (recent) errors in `/data/web/nginx/nginx_error_output`.
* To check if a request was processed by HHVM, you can request a `phpinfo()`. PHP-FPM should show the traditionl phpinfo output, while HHVM will merely say: 'HipHop'.
* If you want to revert all changes and return to the default, just comment all lines in `/data/web/nginx/handler.conf` (and other `.conf` files in `/data/web/nginx/`) and save it. The webserver should reload with the default configuration.


## Known issues

* There is a pending issue which can result in not being able to calculate discounts or tax rates. This can temporarily be solved by installing [this Magento module](https://github.com/danslo/Rubic_FixTotalSorting). A long term solution is being discussed and developed.
* Any Ioncube encoded module is currently, and likely will never be, supported.

