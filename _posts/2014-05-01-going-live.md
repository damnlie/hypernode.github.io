---
layout: post
title:  "Going live"
date:   2014-05-01
excerpt: "Getting your site live on hypernode"
---
You have transfered your shop to hypernode and have tested its functionality, now is the time to go live. Follow these steps to get your site running.

## Prerequisites

* The files and database of your site are copied to the hypernode.
* You have access to the DNS settings of the domains for all your storefronts. Preferably the TTL for the DNS records is reasonably low to make the transition to hypernode quick. Check which domains will need to be configured by listing the base urls. (`n98-magerun sys:store:config:base-url:list`)
* You have configured nginx and tested that your site runs properly on hypernode, including redirects, password protected areas, etc.

## Step 1: Put the site on your old host into maintenance mode

This will prevent from data being changed while performing a last sychronization of the site to the hypernode. Maintenance mode can easily be activated by placing a file called *maintenance.flag* in the application root dir of Magento on your old host. (`touch maintenance.flag`)

## Step 2: Synchronize database and files

Synchronize files and database, using the hypernode importer on hypernode.

```sh
hypernode-importer --host ssh.yourdomain.com --user yourdomain.com --path yourdomain.com
```

The importer will not overwrite the modifications made to the local.xml or fpc.xml configuration files.

If you have custom changes that need to be made to the site after copying to make it run on hypernode, now is the time to apply them.

## Optional Step 3: Take old site out of maintenance mode

If you want to keep maintenance time low at the cost temporarily serving a slow site, you can change the configuration of the old site to use the new database. This step can be safely skipped.

Configuration can be taken from the new local.xml, but this will typically just involve changing the database host to mysqlmaster.*myapp*.hypernode.io.

The website will run quite a bit slower from now on, because database transactions will have to go to a remote data center until the DNS records are changed and traffic starts going to hypernode.

After reconfiguring the database, you can take the site out of maintenance mode by removing the maintenance flag. (`rm maintenance.flag`)

Note that this will perform database transactions on the hypernode, but not the disk changes. Don't upload new media in de Magento backend, because these won't be available on the hypernode!

## Step 4: Take hypernode out of maintenance mode

Remove the maintenance flag on the hypernode, so customers accessing the new site won't hit the maintenance page. (`rm public/maintenance.flag`)

## Step 5: Flush Magento cache on hypernode

Flush the cache of magento on the hypernode. (`n98-magerun cache:flush`)

## Step 6: Change DNS records

### Web records

For all storefront URLS, the DNS records should be changed now. Remove all A records for your web domains, since the IP address of your hypernode might change at any time. The correct configuration is for all domain names is:

```
www.mydomain.com    CNAME             myapp.hypernode.io
mydomain.com        ANAME or ALIAS    myapp.hypernode.io
```

If your DNS provider doesn't support ANAME or ALIAS records, you can use our forwarder to redirect all traffic from your naked domain.com to www.domain.com, by creating the following records:

```
www.mydomain.com    CNAME             myapp.hypernode.io
mydomain.com        A                 46.21.232.141
mydomain.com        A                 46.21.233.172
```

See our manual on [configuring DNS]({% post_url 2014-02-18-configuring-dns %}) for more information.

As the DNS changes are propagated, traffic will go to your hypernode.


### SPF records (for experts)

If you have previously configured SPF, you should also update your the SPF policy. You should include `_spf.hypernode.com` for all domains you choose to send mail from using Hypernode.

The following example allows mail from both your mail exchangers and all your Hypernode nodes:

```
mydomain.com            TXT               v=spf1 mx:mydomain.com include:_spf.hypernode.com ~all
```

## Step 7: Configure cron

Configure cron jobs to run on your hypernode (`crontab -e`), and configure them not to run on your old host (`crontab -ir`).

Hypernode does not provide lockrun but uses [flock](http://linux.die.net/man/1/flock). Replace `lockrun -L` by `flock -n` to make sure that long running cron jobs don't run twice.

Note that the time on hypernode is configured to UTC, so the times used in the crontab are one or two hours later than central european time.

## Optional Step 8: Configure historic backups on hypernode

Hypernode doesn't have historic backups. If you want to have access to periodic backups, this is something you'll have to [manually configure]({% post_url 2014-04-07-historical-backups %}).
