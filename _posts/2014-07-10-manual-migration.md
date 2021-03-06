---
layout: post
title:  "Manually migrating your site to Hypernode"
date:   2014-07-10 11:38:02
categories: migration
excerpt: "Migrating your application manually to your Hypernode"
---


If for some kind of reason the importer-script doens’t work for you (you might use a module that the importer-script isn’t compatible with) you can also migrate your site to Hypernode manually. Follow the
next steps to manually migrate Magento to Hypernode.


**NB:** If you set some environment variables, you can follow along these instructions by copy-pasting most of the commands in this document to the command line. E.g.:

```sh
NODE=name.hypernode.io
DOMAIN=domainname.nl
CURRENT_SSH=$DOMAIN@ssh12345.bytenet.nl
```

1. Add the SSH public key in the [SSH Keymanager](https://service.byte.nl/sshkeymanager/) for your hypernode. Give the key access to both _appname_.hypernode.io and your current domain.

2. Login to your Hypernode using SSH.

    ```sh
    ssh app@$NODE
    ```

3. Make sure you can login to your current domain from there.

    ```sh
    ssh $CURRENT_SSH
    exit
    ```

4. Synchronize all files to the hypernode using rsync

    ```sh
    rsync -a --exclude=/var/\*/\* --delete $CURRENT_SSH:$DOMAIN/ /data/web/public
    ```

    For subsequent runs, you can prevent your xml files from being overwritten by adding `--exclude=\*.xml`

5. Enter /data/web/public, all subsequent steps will assume you are in it.

    ```sh
    cd public
    ```

6. Fix all broken absolute symlinks in the site. You can find them using find:

    ```sh
    find . -type l -xtype l
    modman repair
    ```

7. Change the database host in the local configuration to use the hypernode database server, called 'mysqlmaster':

    ```sh
    sed -i "s/$(n98-magerun db:info host)/mysqlmaster/" app/etc/local.xml
    ```

8. Create the local database user

    ```sh
    DBUSER=$(n98-magerun db:info username)
    DBPASS=$(n98-magerun db:info password)
    DBNAME=$(n98-magerun db:info dbname)

    echo "CREATE DATABASE $DBNAME" | mysql
    echo "GRANT ALL PRIVILEGES ON $DBNAME.* TO '$DBUSER'@'%' IDENTIFIED BY '$DBPASS'" | mysql
    ```

9. Copy the database:

    ```sh
   ssh $CURRENT_SSH "n98-magerun --root-dir=$DOMAIN db:dump --strip='@stripped' --stdout" | n98-magerun db:console
   ```
10. Remove memcache configuration from app/etc/local.xml and fix redis configuration in app/etc/local.xml and app/etc/fpc.xml.

    In app/etc/local.xml the cache configuration should look like this:

    ```xml
    <cache>
        <backend>Cm_Cache_Backend_Redis</backend>
        <backend_options>
            <server>redismaster</server>
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

    And if you have FPC configured, app/etc/fpc.xml should look like this:

    ```xml
    <fpc>
        <lifetime>86400</lifetime>
        <backend>Cm_Cache_Backend_Redis</backend>
        <backend_options>
            <server>redismaster</server>
            <port>6379</port>
            <persistent>cache-fpc</persistent>
            <database>1</database>
            <password></password>
            <force_standalone>1</force_standalone>
            <connect_retries>1</connect_retries>
            <lifetimelimit>86400</lifetimelimit>
            <read_timeout>10</read_timeout>
            <compress_data>1</compress_data>
            <compress_tags>1</compress_tags>
            <compress_data>gzip</compress_data>
        </backend_options>
    </fpc>
    ```

11. Check for hardcoded paths in files and fix them manually. You can find them using:

    ```sh
    grep -r /home/users .
    ```

12. Flush cache, just to be sure :-)

    ```sh
    n98-magerun cache:flush
    ```
    
13. Convert the apache configuration you might have done in .htaccess-files to nginx configuration, see [this manual]({% post_url 2014-02-16-configuring-nginx %}) for some hints.

14. Test your site by creating one or more DNS CNAME records to your hypernode and configuring base urls for them, or by temporarily adding entries to your local hosts file.


15. Copy your cronjobs.

    [More information on cron on your Hypernode]({% post_url 2014-02-12-using-the-hypernode-application-node %}#configure-cron).

    ```sh
    ssh $CURRENT_SSH "crontab -l" | crontab -
    ```

    **NB:** cronjobs often contain full paths to scripts inside your homedirectory. Be sure to replace them for your situation at Hypernode. For example, if your site is currently hosted at Byte, you need to replace ```/home/users/domaiftp``` with ```/data/web/```, and ```/home/users/domaiftp/domain.com``` with ```/data/web/public```.

    **NB:** if you're still testing with Hypernode while your production site is running elsewhere, you might want to wait with the cronjobs until that migration is done. Cronjobs running in multiple places may result in unwanted behaviour.


