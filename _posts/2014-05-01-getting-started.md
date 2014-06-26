---
layout: post
title:  "Migrating your site to Hypernode"
date:   2014-05-01 11:38:02
categories: migration
excerpt: "Migrating your application to your Hypernode"
---
Congratulations, you should be the proud owner of a fresh Hypernode! Now you're wondering how to copy your site from your current hosting environment to Hypernode. Luckily, we've automated most of the work for you. You can use our hypernode-importer script or migrate the site manually. Instructions about both methods can be found below:

## Prerequisites
* A Hypernode application, called _name_.hypernode.io in the Byte Service Panel
* An SSH public/private keypair
* An SSH agent with the private key loaded
    * On Linux or Mac OS you can check this using `ssh-add -L`
    * On Windows there should be an icon for ''putty-agent'' in the system tray, with your key
* Enable SSH Agent Forwarding in your config.
    * Linux/Mac: `echo "ForwardAgent yes" >> ~/.ssh/config`
    * Windows: [follow these instructions](http://www.howtogeek.com/125364/how-to-ssh-hop-with-key-forwarding-from-windows/)
* SSH access to your current hosting environment, using the private key in the agent
* The documentroot of your site in the current hosting environment
    * This is the place where the files for your site are located, and is (most of the times) a subdirectory in your homedirectory
* [n98-magerun](http://magerun.net/installation/) should be installed in the current hosting environment, and be available using `n98-magerun`
    * This is covered if your site is currently hosted at Byte

## Add your public key to the SSH Keymanager

Before using the hypernode-importer script or migrating your site manually, you should make sure that your public key is added to the SSH Keymanager in the Byte Service Panel. Follow these steps to get this done:


1. Log in to the [Byte Service Panel](https://service.byte.nl/protected/overzicht/)
1. Select your domainname (name.hypernode.io)
1. Click on the option ''SSH Toegang''
1. Click on the ''SSH key toevoegen'' option. If you already have a key configured, you should click on the ''Beheer mijn public keys'' option. After that, click on the ''SSH key toevoegen’’ option
1. Paste the content of your public key into the public key field and give it a name (title field)
1. Click on ''Toevoegen'' to add the key to the Key Manager
1. Select the domain you wish to add your key to (if you’re a Byte customer, make sure you add the key to your hypernode domain and the Magento domain the site is currently being hosted) and click on the ''Opslaan'' button
1. Your key is now added to your domains. You can now use the hypernode-importer script or manually migrate your site.

## Using the hypernode-importer script

We’ve created a script that can migrate your site to your Hypernode account. Use the following steps to migrate your shop to the Hypernode environment:


1. Log in to your Hypernode
    * `ssh app@name.hypernode.io`
    * If you're being asked to enter a password, either your private key is not loaded in your SSH Agent, you haven't given the public key access to the Hypernode, or the public key hasn't propagated to the Hypernode yet. The distribution of the key to the Hypernode may take a minute.
1. Use `hypernode-importer` to automatically migrate your site to your Hypernode. It's usage is simple:
    * `hypernode-importer --host SSHHOST --user SSHUSER --path PATH`
    * `hypernode-importer --help` for a full list of options.
1. If your site is hosted at Byte, the command would look like this:
    * `hypernode-importer --host ssh.domain.com --user domain.com --path domain.com`
1. Sit back while we do the work :)

If anything goes wrong (authentication, error during copy), you can just run the importer again after fixing the problem, or manually migrate your site to hypernode.

## Manually migrating your site to Hypernode

If for some kind of reason the importer-script doens’t work for you (you might use a module that the importer-script isn’t compatible with) you can also migrate your site to Hypernode manually. Follow the next steps to manually migrate your site to Hypernode.


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
    ```
    
    If these are created by modman they can possibly be automatically repaired using:

    ```sh
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


## Optional steps: configuring SSL 

If you currently have SSL certificates and your site is hosted at Byte, you should make sure you select the SSL certificate to use in the Byte [Service Panel](https://service.byte.nl/). For more information about configuring your SSL certificate in hypernode, please read the article about [Using SSL with Hypernode]({% post_url 2014-06-17-using-ssl-with-hypernode %}).

## The next steps

1. Test your site on hypernode.
1. [go live]({% post_url 2014-05-01-going-live %})
