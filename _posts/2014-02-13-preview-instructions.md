---
layout: post
title:  "Magento Hypernode Preview Instructions"
date:   2014-02-13 13:55:03
categories: manual usage 
excerpt: "A checklist for pilot participants."
---
## Welcome to the Hypernode pilot project!

Your new Hypernode is ready to boost your customer's shop! Notable advantages:

1. Speedier, thanks to a new PHP version, opcache cache, local storage, local database access, dedicated query cache, low latency local Redis.
1. No BPU metering, no more PHP execution time limit.

After [copying your site to Hypernode]({% post_url 2014-05-01-getting-started %}), your site should be available using `<name>.hypernode.io`. If you get redirected to the production site, you might need to make some changes to your store configuration.

### Procedure

1. You are invited to browse your customer's shop on the given preview address. Use the checklist below to verify its functioning.
1. When you are happy with the results, send us an OK.
1. We will issue a re-sync of your files and data and change the DNS for your domain(s) to the new node.
    * In case of external DNS control, we will propose a specific timeslot, as the migration of data and DNS has to happen at the same time.

If you find anything missing, please discuss it with us @ `support@hypernode.com`.

### Changes

#### Changes to the software stack

Hypernodes run on a complete different software stack. This means that some minor functionality might differ from the current platform. Notable differences:

1. Database and redis server names have changed to `mysqlmaster.<tag>.hypernode.io` and `redismaster.<tag>.hypernode.io`.
1. Our new webserver Nginx does not support `.htaccess` files (so it is faster). We handle most Magento .htaccess functionality out of the box, but if you have added custom functionality to one or more htaccess files, you should check that [it works under Nginx](configuring-nginx.html). 
    1. Client IP blocks
    1. Folders with password protection
    1. URL redirects
    1. URL rewrites
    1. Magento RUN CODES and TYPES.
1. All file uploads should be done by using SCP, RSYNC or SFTP. Regular FTP is disabled for security reasons. You should use host `<tag>.hypernode.io` and user `app` for SSH and SCP operations. 
1. Password access is disabled. The nodes use your SSH public keys that you can manage on the Byte [service panel](https://service.byte.nl/sshkeymanager/). This is installed once when we put your preview shop on Hypernode. If you make changes, mail us to reinstall your keys.
1. Redis doesn't require a password.
1. Installed PHP is version 5.4. Magento 1.6+ officially supports it, but some versions require a patch. See the [Magento announcement](http://www.magentocommerce.com/blog/magento-now-supports-php-54/) for the patch.
1. We backup in case of hardware failures, but there are (as of yet) no historical backups. If you need them, we advice you to use the supplied program with offsite storage (such as S3). **TODO** insert historical backup recommendation.
1. New Relic is temporarily disabled (it's on our todo list).
1. The absolute path to the document root has changed to /data/web/public. Non-public files (such as credentials) should go into /data/web.
1. The outgoing IP of your shop changes. This might affect external systems (such as inventory or supplier systems) if you have previously given them an IP.
1. The system time is set to [UTC](http://en.wikipedia.org/wiki/Coordinated_Universal_Time) (previously it was set to Dutch time with daylight saving enabled). This resolves issues with foreign customers, and also synchronising with foreign servers. If you need a specific timezone, please set it explicitly ([PHP example](http://nl1.php.net/manual/en/function.date-default-timezone-set.php)). Note: this also applies to cronjob timings. If you need to adjust your crons for DST, you should write a wrapper. It is on our todo list to fix this.

#### Things we've converted for you

While setting up your preview environment, we've automatically converted the following things. Take these into account if you run a git clone of your codebase on our servers (and preferably merge those changes into your own repo). 

1. Any hardcoded paths found (starting with /home/users) are automatically replaced (in text files and your database data). Absolute paths in binary data (such as SQLite databases) might not have been replaced, so you should check those.
1. Symlinks pointing to hardcoded paths are replaced with their relative counterparts.
1. Your `local.xml` is updated so it contains the new database and redis hostnames.

Specifically for the preview, we've changed some temporary things. Those will obviously not be applied when your live shop migrates.

* Base URLs for your store fronts are updated in the database (so you'll be able to test using the *.hypernode.io domain). 
* A very restrictive `robots.txt` is installed so your preview shop won't get indexed by Google and others. 

<a name="preview-checklist"></a>
### Checklist

_Important_: When browsing your preview shop, doublecheck that you don't end up on your production shop by following a hardcoded link. 

- ☐  Test all storefronts
- ☐  Test frontpage, categories, product pages
- ☐  Test search
- ☐  Test restricted areas (downloads, etc).
- ☐  Test ordering a product
- ☐  Test common tasks in the admin backend (ie, add product)
- ☐  Test import/export procedures
- ☐  Test custom administrative procedures
- ☐  Test if your app is able to send mails
- ☐  Test that crons work
- ☐  Test that extra software (such as Wordpress) works as well.
- ☐  Verify that connections with external systems (inventory, analytics, etc) still work.
- ☐  Do you have a historical backup of the database?
- ☐  Do you have (need) a historical backup of your files or can you redeploy from Git?

### Changes summary

|  | Byte | Hypernode |
| :--- | :--- | :--- |
| __Backups__ | Historical | Recovery |
| __PHP Version__ | 5.3, php-cgi | 5.4, php-fpm |
| __Webserver__ | Apache | Nginx |
| __Speed__ | *** | ***** |
| __Absolute path__    | /home/users/domaiftp | /data/web |
| __Document root__&nbsp;&nbsp;&nbsp;| /home/users/domaiftp/domain.nl/&nbsp;&nbsp;&nbsp;| /data/web/public |
| __Upload__ | FTP, FTPS, SSH (SFTP, SCP, rsync) | SSH (SFTP, SCP, rsync) |
