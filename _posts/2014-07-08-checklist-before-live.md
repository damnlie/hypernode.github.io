---
layout: post
title:  "Checklist"
date:   2014-07-08 13:55:03
categories: manual usage
excerpt: "A checklist for customers who want to check everything before going live with their hypernode account."
---

## Things we've converted for you

While setting up your preview environment, we've automatically converted the following things. Take these into account if you run a git clone of your codebase on our servers (and 
preferably merge those changes into your own repo).

1. Any hardcoded paths found (starting with /home/users) are automatically replaced (in text files and your database data). Absolute paths in binary data (such as SQLite databases) might not have been rep$
1. Symlinks pointing to hardcoded paths are replaced with their relative counterparts.
1. Your `local.xml` is updated so it contains the new database and redis hostnames.

Specifically for the preview, we've changed some temporary things. Those will obviously not be applied when your live shop migrates.

* Base URLs for your store fronts are updated in the database (so you'll be able to test using the *.hypernode.io domain).
* A very restrictive `robots.txt` is installed so your preview shop won't get indexed by Google and others.

<a name="preview-checklist"></a>

## Testing your preview shop

Before changing the DNS settings of your original domain to Hypernode, it's important to first check if everything in your shop is working as it should. 

Use the following steps to test everything in your shop:

***NB:*** When browsing your preview shop, doublecheck that you don't end up on your production shop by following a hardcoded link.

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

If every box had been checked, you can change the DNS settings of your original domain to Hypernode. Instructions on how to do this, can be found in the article
 [Go live with your Hypernode]({% post_url 2014-05-01-going-live %})
