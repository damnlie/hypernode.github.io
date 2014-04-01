---
layout: post
title:  "Using MySQL on the Hypernode Application Node"
date:   2014-02-12 11:55:03
categories: manual usage mysql
excerpt: "What you need to know about using MySQL on Hypernode.
How to find your credentials and how to create and use databases."
---
## Finding your credentials

Your MySQL credentials are stored in the homedir of application user. You find them in the file `~/.my.cnf`.

```ini
[client]
host = mysqlmaster.<tagname>.hypernode.io
user = app
password = JlogA1Sws6XMHmAj7QlP9vpfjLprtpE5
```

## What you should know

* There is no predefined database, so you should create your own.
* The `app` user is the local superuser. This means you can (among other things):
    * can create your own databases;
    * create users;
    * define views and triggers.
* MySQL is not reachable from the internet. That means that you'll need to [setup
a (temporary) SSH-tunnel](#sshtunnel) if you need to access MySQL from another host.


## How to connect to MySQL

### Using the command line shell on the production node

Because we've provided a `~/.my.cnf`, you're all set to go. Just type `mysql` and you're in.

```bash
mysql
```


### Using the command line shell from a remote host

1. [Setup an SSH tunnel](#sshtunnel)
1. Use your credentials to connect like so:

```bash
mysql --host=mysqlmaster.<tagname>.hypernode.io --user=app --password=mypassword
```

### <a name="sshtunnel"></a>Setting up an SSH tunnel to connect from another host

#### MacOS, Linux

MacOS X and Linux have a pre-installed SSH-client. Just fire up a terminal and type:

```bash
ssh -L 3306:localhost:3306 app@<tagname>.hypernode.io
```

This will tunnel MySQL port 3306 from your node, to port 3306 on your own machine.
Now, you should be able to connect to MySQL on your own machine on 127.0.0.1, port 3306.
Open up a new terminal and run:

```bash
mysql --host=127.0.0.1 --user=app --password=mypassword
```

If you have a MySQL server running on your own machine for development, you might get
an error like `bind: Address already in use`. Just choose another port to tunnel to:

```bash
ssh -L 33306:localhost:3306 app@<tagname>.hypernode.io
```

And in a new terminal:

```bash
mysql --host=127.0.0.1 --port 33306 --user=app --password=mypassword
```

**Note**: `localhost` means something else to the mysql-client than `127.0.0.1`.
`localhost` means a connection through a local socket (most of the time in
`/var/lib/mysql`). Since the your MySQL is not actually running on localhost, you need
to connect to `127.0.0.1`.


### Using HeidiSQL to connect to MySQL

First, go to the [HeidiSQL](http://www.heidisql.com/download.php) homepage and use the installer to install
HeidiSQL. Then configure HeidiSQL as follows:

1. [Setup an SSH tunnel](#sshtunnel)
1. Start HeidiSQL
1. Click `new` in the bottom-left corner
1. Choose a suitable name for your connection
1. Edit the following fields:
    1. `Hostname / IP`: 127.0.0.1
    1. `User`: enter the MySQL user (see credentials)
    1. `Password`: enter the MySQL password for the user (see credentials)
1. Leave all other fields as they are.
1. Click `save` in the bottom-left corner

Things should look like the screenshot below:

![HeidiSQL configured for Hypernode]({{ site.url }}/assets/hypernode-heidisql.png)


## Creating a MySQL backup

### <a name="magerun"></a>Using Magerun

Use the following command using SSH:

```bash
 magerun db:dump -n -c gz -s @stripped
```

This will create a compressed SQL file suitable for importing using either Magerun or `mysqlclient`. The dump will exclude any logfiles or temporary import tables.


### Using mysqldump

You should consider using [Magerun](#magerun), but if you need to, you can create a mysqldump on the shell. See our documentation at [byte.nl](http://www.byte.nl/wiki/Database_exporteren_en_importeren) (dutch).


### Using HeidiSQL

You should consider using [Magerun](#magerun), but you could use HeidiSQL to create a database dump.

1. [Setup an SSH tunnel](#sshtunnel)
1. Start HeidiSQL.
1. Connect to your node.
1. Use Tools -> Export database as SQL.
1. In the left pane, choose the Magento database.
1. Choose a filename to export to.
1. Choose appropriate options, notably `data`.
1. Press Export.
