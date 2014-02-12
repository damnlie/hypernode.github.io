---
layout: post
title:  "Hypernode Application Node Manual"
date:   2014-02-12 11:55:03
categories: manual usage
---
## Uploading data

### Uploading files

* The documentroot for web files is `/data/web/public/`.
* Place configuration files or secret files outside of your documentroot in `/data/web/`.
* Use one of the SSH-based file transfer protocols below to upload files.
    * SFTP
    * SCP
    * rsync

### SSH

* Use the SSH key manager at the [Byte Service Panel](https://service.byte.nl/sshkeymanager/) to manage your keys.
* There is no password authentication.

### MySQL

MySQL is open at port `3306`. Use your credentials to connect.


## Access to logs

There are several logs for you to access:

Logtype | Location
-|-
Web logs | `/var/log/nginx/access.log`
Web error logs | `/var/log/nginx/error.log`
PHP logs | Not yet provided
MySQL slow log | `/var/log/mysql/mysql-slow.log`
MySQL error log | `/var/log/mysql/error.log`


## Using MySQL

* MySQL is on `localhost`.
* Your credentials are in `/data/web/.my.cnf`.

There is no predefined database, so you should create your own.

You are a local superuser. This means you can (among other things):
* can create your own databases;
* create users;
* define views and triggers.


## Using Redis

* Redis runs on `localhost.
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
