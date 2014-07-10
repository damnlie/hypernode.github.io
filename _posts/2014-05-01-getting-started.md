---
layout: post
title:  "Migrating your site to Hypernode"
date:   2014-05-01 11:38:02
categories: migration
excerpt: "Migrating your application to your Hypernode"
---
Congratulations, you should be the proud owner of a fresh Hypernode! Now you're wondering how to copy your site from your current hosting environment to Hypernode. Luckily, we've automated most 
of the work for you. You can use our hypernode-importer script. Instructions can be found below:


1. Add your public key to your Hypernode with the [SSH Keymanager]({% post_url 2014-07-10-using-ssh-keymanager %}).
1. Log in to your Hypernode
    * `ssh app@name.hypernode.io`
1. Use `hypernode-importer` to automatically migrate your site to your Hypernode. It's usage is simple:
    * `hypernode-importer --host ssh.domain.com --user domain.com --path domain.com`
1. Sit back while we do the work :)

If anything goes wrong (authentication, error during copy), you can just run the importer again after fixing the problem, or [manually migrate]({% post_url 2014-07-10-manual-migration %}) your site to hypernode.

