---
layout: post
title:  "Getting started with your Hypernode"
date:   2014-05-01 11:38:02
categories: migration
excerpt: "Migrating your application to your Hypernode"
---

Congratulations, you should be the proud owner of a fresh Hypernode! Now you're wondering how to copy your site from your current hosting environment to Hypernode. Luckily, we've automated most of the work for you.

## Prerequisites
* A Hypernode application, called _name_.hypernode.io in the Byte Service Panel
* An SSH public/private keypair
* An SSH agent with the private key loaded
    * On Linux or Mac OS you can check this using `ssh-add -L`
    * On Windows there should be an icon for ''putty-agent'' in the system tray, with your key
* SSH access to your current hosting environment, using the private key in the agent
* The documentroot of your site in the current hosting environment
    * This is the place where the files for your site are located, and is (most of the times) a subdirectory in your homedirectory
* [n98-magerun](http://magerun.net/installation/) should be installed in the current hosting environment, and be available using `n98-magerun`
    * This is covered if your site is currently hosted at Byte

## Steps
1. Go to the [SSH Keymanager](https://service.byte.nl/sshkeymanager/)
1. Add the public key if it isn't already there
1. Give the key access to the Hypernode domain (_name_.hypernode.io)
1. If your site is currently hosted at Byte, also give the key access to the site that will be copied
1. Log in to your Hypernode
    * `ssh app@name.hypernode.io`
    * If you're being asked to enter a password, either your private key is not loaded in your SSH Agent, you haven't given the public key access to the Hypernode, or the public key hasn't propagated to the Hypernode yet. The distribution of the key to the Hypernode may take a minute.
1. Use `hypernode-importer` to automatically migrate your site to your Hypernode. It's usage is simple:
    * `hypernode-importer --host HOST --user USER --path PATH`
    * `hypernode-importer --help` for a full list of options
1. Run the importer with the credentials to you current hosting environment. If your site is hosted at Byte, the command would look like this:
    * `hypernode-importer --host ssh.domain.com --user domain.com --path domain.com`
1. Sit back while we do the work :)

If anything goes wrong (authentication, error during copy), you can just run the importer again after fixing the problem.

## Optional steps
1. If you currently have SSL certificates and your site is hosted at Byte, you should make sure you select the SSL certificate to use in the Byte [Service Panel](https://service.byte.nl/). Go to _Hypernode Settings_, then select your SSL certificate at _SSL information_.

## From here
Wondering what the **** just happened? Or did something go wrong and you need to do something by hand? `hypernode-importer` automates steps 1 to 12 (flushing the  cache) from [this list of manual migration steps]({% post_url 2014-04-11-migrating-to-hypernode %}).

The next steps, in a nutshell, are:

1. [previewing the site]({% post_url 2014-02-13-preview-instructions %})
1. [make sure everything works as planned]({% post_url 2014-02-13-preview-instructions %}#preview-checklist)
1. [go live]({% post_url 2014-05-01-going-live %})
