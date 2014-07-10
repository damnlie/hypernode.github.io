---
layout: post
title:  "How to use Nginx"
date:   2014-02-16 14:10:58
categories: manual usage nginx
excerpt: "Information on how to use Nginx on Hypernode."
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

Some typical scenarios are explained below:

- <a href="{% post_url 2014-07-10-nginx-run-codes-types %}">Configuring run codes/run types</a><br/>
- <a href="{% post_url 2014-07-10-nginx-blocking-allowing-ip-addresses %}">Blocking/allowing IP-addresses</a><br/>
- <a href="{% post_url 2014-07-10-protecting-directory-with-password %}">Protecting a directory with a password</a><br/>
- <a href="{% post_url 2014-07-10-rewriting-redirecting-urls %}">Rewriting/redirecting URLs</a><br/>
- <a href="{% post_url 2014-07-10-setting_server_name %}">Setting the server name</a><br/>


