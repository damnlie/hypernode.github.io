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

* Use the [SSH keys manager](https://service.byte.nl/sshkeymanager/) at the Byte Service Panel to manage your SSH-keys. These are installed once when we put your preview shop on Hypernode. If you make changes, mail us to reinstall your SSH-keys.
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

### Magento Redis backend

Use the following snippet in your `local.xml` (replace XXXXX by your tagname):

```xml
        <cache>
            <backend>Cm_Cache_Backend_Redis</backend>
            <backend_options>
                <server>redismaster1.XXXXX.hypernode.io</server>
                <port>6379</port>
                <persistent></persistent>
                <database>0</database>
                <password></password>
                <force_standalone>0</force_standalone>
                <connect_retries>1</connect_retries>
                <read_timeout>10</read_timeout>
                <automatic_cleaning_factor>0</automatic_cleaning_factor>
                <compress_data>1</compress_data>
                <compress_tags>1</compress_tags>
                <compress_threshold>20480</compress_threshold>
                <compression_lib>gzip</compression_lib>
            </backend_options>
        </cache>
```


## Flushing caches

### Magento files cache backend

Use the button in the Magento admin backend, or enforce by removing all files in `/data/web/public/var/cache/`.

### Redis

Use the command line (SSH) to flush the Redis cache:

```bash
# flush all keys in all databases
redis-cli flushall

# flush all keys in database 0 or database 1 respectively
redis-cli -n 0 flushdb 
redis-cli -n 1 flushdb 
```

### PHP Opcode Cache (OpCache)

You cannot flush the opcache, but it will automatically detect changes on the filesystem, and reload when necessary.
