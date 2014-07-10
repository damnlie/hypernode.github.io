---
layout: post
title:  "Checklist"
date:   2014-07-08 13:55:03
categories: manual usage
excerpt: "A checklist for customers who want to check everything before going live with their Hypernode account."
---

Before changing the DNS settings of your original domain to Hypernode, it's important to first check if everything in your shop is working as it should. 
	
Use the following steps to test everything in your shop:

***NB:*** When browsing your preview shop, doublecheck that you don't end up on your production shop by following a hardcoded link. If you'd like to print the checklist, use the following link: 
[checklist.pdf]({{ site.url }}/assets/checklist.pdf).

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

If every box has been checked, you can change the DNS settings of your original domain to Hypernode. Instructions on how to do this, can be found in the article 
[Go live with your Hypernode]({% post_url 2014-05-01-going-live %})
