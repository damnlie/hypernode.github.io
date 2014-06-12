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

__NB: nginx has a complex but [well documented](http://nginx.org/en/docs/http/ngx_http_core_module.html#location) order in which `location` directives are checked.__



### Scenario 1: run your entire site in HHVM

Our basic configuration dictates that each file ending with `.php` will be parsed by PHP-FPM. This is a safe default. In `handler.conf` you may specify that all files ending with `.php` should be handled by HHVM:

```
location ~ \.php$ {
    echo_exec @hhvm;
}
```

### Scenario 2: run your site in HHVM, but run the API in plain PHP


```
# Run the API in PHP
location /api/soap {
    rewrite / /index.php break;
    echo_exec @phpfpm;
}
# Another API location
location ^~ /index.php/api/soap {
    rewrite / /index.php break;
    echo_exec @phpfpm;
}
# Run the rest in HHVM
location ~ \.php$ {
    echo_exec @hhvm;
}
```


### Scenario 3: keep your site running in PHP, but run a couple of URLs in HHVM as a test

```
# The default is to run in PHP, so no need to specify that

# Run these two locations in PHP
location /apparel/hoodies.html {
    rewrite / /index.php break;
    echo_exec @hhvm;
}
location ^~ /index.php/apparel/hoodies.html {
    rewrite / /index.php break;
    echo_exec @hhvm;
}
```


### Scenario 4: run only all PHP-files in a subdirectory in HHVM

```
# Run everything that starts with this location
location /blog/ {
    location ~ \.php$ {
        echo_exec @hhvm;
    }
}
```

### Scenario 5: have your site run in HHVM, but run all PHP files in a subdirectory in plain PHP

```
# Set the default interpreter to PHP for every URL that starts with /blog/
location /blog/ {
    location ~ \.php$ {
        echo_exec @phpfpm;
    }
}
# But set the overall default to PHP
location ~ \.php$ {
    echo_exec @hhvm;
}
```

## Troubleshooting
* If your change doesn't seem to work, first check that there are no (recent) errors in `/data/web/nginx/nginx_error_output`.
* To check if a request was processed by HHVM, you can request a `phpinfo()`. PHP-FPM should show the traditionl phpinfo output, while HHVM will merely say: 'HipHop'.
* If you want to revert all changes and return to the default, just comment all lines in `/data/web/nginx/handler.conf` (and other `.conf` files in `/data/web/nginx/`) and save it. The webserver should reload with the default configuration.


## Known issues

* There is a pending issue which can result in not being able to calculate discounts or tax rates. This can temporarily be solved by installing [this Magento module](https://github.com/danslo/Rubic_FixTotalSorting). A long term solution is being discussed and developed.
* Any Ioncube encoded module is currently, and likely will never be, supported.

