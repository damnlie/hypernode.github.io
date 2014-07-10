---
layout: post
title:  "Protecting a directory with a password in Nginx"
date:   2014-07-10 14:10:58
categories: manual usage nginx
excerpt: "Instructions on how to protect a directory wioth a password in Nginx."
---

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

