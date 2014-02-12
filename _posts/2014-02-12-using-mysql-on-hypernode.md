---
layout: post
title:  "Using MySQL on the Hypernode Application Node"
date:   2014-02-12 11:55:03
categories: manual usage mysql heidisql
---
## Finding your credentials

Your MySQL credentials are stored in the homedir of application user. You find them in the file `~/.my.cnf`.

```ini
[client]
host = mysqlmaster1.tagname.hypernode.io
user = app
password = JlogA1Sws6XMHmAj7QlP9vpfjLprtpE5
```

## What you should know

* There is no predefined database, so you should create your own.
* The `app` user is the local superuser. This means you can (among other things):
    * can create your own databases;
    * create users;
    * define views and triggers.


## How to connect to MySQL

### Using the command line shell on the production node

Because we've provided a `~/.my.cnf`, you're all set to go. Just type `mysql` and you're in.

```bash
mysql
```


### Using the command line shell from a remote host

Use your credentials to connect like so:

```bash
mysql --host=mysqlmaster1.tagname.hypernode.io --user=app --password=mypassword
```


### Using HeidiSQL to connect to MySQL

First, go to the [HeidiSQL](http://www.heidisql.com/download.php) homepage and use the installer to install
HeidiSQL. Then configure HeidiSQL as follows:

1. Start HeidiSQL
1. Click `new` in the bottom-left corner
1. Choose a suitable name for your connection
1. Edit the following fields:
    1. `Hostname / IP`: enter the MySQL hostname (see credentials)
    1. `User`: enter the MySQL user (see credentials)
    1. `Password`: enter the MySQL password for the user (see credentials)
1. Leave all other fields as they are.
1. Click `save` in the bottom-left corner

Things should look like the screenshot below:

![HeidiSQL configured for Hypernode]({{ site.url }}/assets/hypernode-heidisql.png)

