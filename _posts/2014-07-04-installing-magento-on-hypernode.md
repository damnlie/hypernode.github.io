---
layout: post
title:  "Installing Magento on Hypernode"
date:   2014-07-04 16:40:02
categories: installation
excerpt: "A guide to install a fresh Magento on your Hypernode"
---

You don't have a Magento shop yet to migrate to Hypernode? We've got ya covered, buddy ;-) You can install a brand new fresh Magento shop on your Hypernode by following the steps mentioned in this article.

## Create a new database

The first step should be to create a database in which Magento can store all it's data in. Luckily for you there is a file where most of the database credentialsis is already stored; .my.cnf. 
So you only need to use the "mysql" command to connect to MySQL. Next step is to create a database. You can do this by using the next command:

```sh
   CREATE DATABASE DATABASENAME  
  ``` 

After this you exit mysql by using "exit;" . Write down your database name or add it to the .my.cnf file. Just make sure you can use it later.

## Installing Magento with n98-magerun

1. To install Magento you'll have to go to your "public" folder. Use the following command to install Magento through n98-magerun:

    ```sh
    php -d memory_limit=1024M /usr/local/bin/n98-magerun install
    ```

2. Select the version you wish to install. The first option [1] is the most recent version of Magento. Press "Enter". 

3. Select the folder where you'd like to install Magento in. 

4. Enter your database credentials.

5. Press "y" or "n" if you want to install sample data. If you told the script you'd like to have some sample data, better grab a cup of coffee or tea. This might take a while ;-)

6. You're asked to fill in some information to finish the installation. You have to submit the following information:
6.1  session save<br>
6.2  admin frontname<br>
6.3  default currency code<br>
6.4  locale code<br>
6.5  timezone<br>
6.6  admin username<br>
6.7  admin password<br>
6.8  admin's firstname<br>
6.9  admin's lastname<br>
6.10 admin's email<br>
6.11 base url<br>

7. After this, the script will install Magento for you.

8. When the installation is complete, you're asked if you want write the baseURL to your .htaccess file? Press "n" and the script will reindex all data in your shop.

9. The installation is complete. You're presented with all the checks the script did to ensure Magento was successfully installed on your Hypernode.

## Move the Magento files to the right folder

If you followed this guide, your magento files are now located in /data/web/public/magento . Move these files to the /data/web/public folder and delete the magento folder after this. 
Your shop will now be visible through your Hypernode URL.

## Configure Redis as Cache Backend

Follow these steps to change your caching backend to Redis:

1. Go to your public folder; /data/web/public

2. Use the following commands to install the Redis module on your shop:

   ```sh
    modman init
    modman clone git://github.com/colinmollenhour/Cm_Cache_Backend_Redis.git
    ```

3. Open your local.xml file and paste the next lines of code after the closing crypt tag:

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

4. Flush your cache after making these adjustments:

   ```sh
    n98-magerun cache:flush
   ```

## Optional: configure Lesti::FPC

If you'd like to use Lesti::FPC on Fypernode, please follow the next steps:

1. Go to your public folder; /data/web/public

2. Use the following command to install the Lesti::FPC module on your shop:

   ```sh
   modman clone git://github.com/GordonLesti/Lesti_Fpc.git
    ```
 
3. Go to the .modman/Lesti_Fpc/app/etc folder and copy the fpc.xml.sample and rename it to fpc.xml . 

4. Move the fpc.xml file to the /public/app/etc folder.
 
5. Open the file and delete everything between the fpc tags.

6. In between the fpc tags, paste the following lines of code:

   ```xml
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
    ```   

7. Flush your cache after making these adjustments:

   ```sh
    n98-magerun cache:flush
   ```

