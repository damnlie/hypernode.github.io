---
layout: post
title:  "Using the SSH Keymanager"
date:   2014-07-10 11:38:02
categories: tools
excerpt: "Instructions on how to use the SSH Keymanager"
---

Before using the hypernode-importer script or migrating your site manually, you should make sure that your public key is added to the SSH Keymanager in the Byte Service Panel. Follow these steps to
get your SSH key configured:

1. Log in to the [Byte Service Panel](https://service.byte.nl/protected/overzicht/)
1. Select your domainname (name.hypernode.io)
1. Click on the option ''SSH Toegang''
1. Click on the ''SSH key toevoegen'' option. If you already have a key configured, you should click on the ''Beheer mijn public keys'' option. After that, click on the ''SSH key toevoegen’’ option
1. Paste the content of your public key into the public key field and give it a name (title field)
1. Click on ''Toevoegen'' to add the key to the Key Manager
1. Select the domain you wish to add your key to (if you’re a Byte customer, make sure you add the key to your hypernode domain and the Magento domain the site is currently being hosted) and click 
on the "Opslaan" button.
1. Your key is now added to your domains. You can now use the [hypernode-importer]({% post_url 2014-05-01-getting-started %}) script or [manually migrate]({% post_url 2014-07-10-manual-migration %}) your site.

If anything goes wrong (authentication, error during copy), you can just run the importer again after fixing the problem, or manually migrate your site to hypernode.

