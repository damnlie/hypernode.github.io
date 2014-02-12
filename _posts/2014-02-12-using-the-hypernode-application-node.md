---
layout: post
title:  "Hypernode Application Node Manual"
date:   2014-02-12 11:55:03
categories: manual usage
excerpt: "A short manual covering how to use SSH, how to upload files, how to use Redis, etc."
---
## Uploading data

### Uploading files

* The documentroot for web files is `/data/web/public/`.
* Place configuration files or secret files outside of your documentroot in `/data/web/`.
* Use one of the SSH-based file transfer protocols below to upload files. Use `app` as username and `<tag>.hypernode.io` as server address.
    * SFTP
    * SCP
    * rsync

### SSH

* Use the [SSH key manager](https://service.byte.nl/sshkeymanager/) at the Byte Service Panel to manage your keys.
* There is no password authentication.


## Access to logs

There are several logs for you to access:

Logtype | Location
--- | ---
Web logs | `/var/log/nginx/access.log`
Web error logs | `/var/log/nginx/error.log`
PHP logs | Not yet provided
MySQL slow log | `/var/log/mysql/mysql-slow.log`
MySQL error log | `/var/log/mysql/error.log`


## Using MySQL

See [Using MySQL on Hypernode]({% post_url 2014-02-12-using-mysql-on-hypernode %}) on how to use MySQL.


## Using Redis

* Redis runs on `redismaster1.<tag>.hypernode.io`.
* It runs on port `6379`.
* There is no need for a password.
* We've predefined databases `db0` and `db1` so you can flush sessions and key seperately.

### Flusing the cache

Use the command line (SSH) to flush the Redis cache:

```bash
# flush all keys in all databases
redis-cli flushall

# flush all keys in database 0 or database 1 respectively
redis-cli -n 0 flushdb 
redis-cli -n 1 flushdb 
```

## Flushing caches

### Magento files cache backend

Remove all files in `/data/web/public/var/cache/`.

### Magento Redis backend

See `Redis`.

### PHP APC cache

You cannot flush the APC backend.
