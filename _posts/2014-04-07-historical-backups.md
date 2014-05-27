---
layout: post
title:  "Historical backups"
excerpt: "At the time of writing (April 2014), Hypernode fully covers recovery backups, which are taken daily. There are no historical backups in place yet. Meanwhile, we provide instructions on this page on how to set up periodic backup, possibly copied to an offsite location."
---

In general there are two types of backups: 

* __Recovery__, so that in case of a serious system failure (fire, earthquake, EMP), the system can be restored to the most recent backup
* __Historical__, over time multiple snapshots are taken. When things go _slightly_ wrong (ie. a product is accidentally deleted), one can go back in time to restore the appropriate snapshot (or part thereof).

At the time of writing (April 2014), Hypernode fully covers recovery backups, which are taken daily. There are no historical backups in place yet. It is on [our roadmap](http://hypernode.uservoice.com/forums/242854-hypernode/filters/top) though. Meanwhile, we provide instructions on this page on how to set up periodic backup, possibly copied to an offsite location. 

# Database 

Most important volatile data is the database. 

### Creating 

Put the following command in your cron. It will not lock your database, so it can be safely run at busy times, howevert his example runs nightly.

```bash
MAILTO=yourmailadress@here
0 2 * * * mkdir -p ~/backup; flock -n ~/.mysqldump chronic /usr/local/bin/n98-magerun db:dump --root-dir=~/public --compression=gz --no-interaction --strip @stripped ~/backup
0 5 * * * chronic find /data/web/backup/ -type f -mtime +7 -delete
```

Notes:
* This requires some free space on your Hypernode. For a typical 1G database, the compressed dump file takes 20M (the index data is not copied). You can use ```df -h ~``` to verify the available amount of space.
* Chronic will only mail the output, if there is a failure.
* Flock ensures that only a single instance runs. If, for some reason, a backup run takes a long time, it will never run twice at the same time.

### Restoring

```bash
n98-magerun db:import --root-dir=~/public <backup_filename>
```

# Offsite backup

If you want to copy your data to an offsite location, we recommend the external "tarsnap" service. It encrypts your data and then stores it on Amazon S3 storage platform. S3 is extremely reliable, as the data is duplicated at at least three different data centers. Nobody but you has access to the data, as it is encrypted using the best open standards. Not even the NSA can get to it. 

Tarsnap charges $0.25 per GB per month, but only for new data (incremental updates). 

### How to get up and running in 10 minutes

First, register for an account at [tarsnap](http://www.tarsnap.com/) and upload some prepaid funds. For an average Magento shop, &euro;100 will pay for a year of backup service. 

Second, you will need a private key to encrypt your data. On your Hypernode, run ```tarsnap-keygen --keyfile ~/tarsnap.key --user <email> --machine <mysite>```. You should copy this ```tarsnap.key``` file to a safe location, as your backup is inaccessible without it! A local USB stick is a good idea.

Third, create a tarsnap configuration file:

```bash
mkdir -p ~/.tarsnapcache
cat > ~/.tarsnaprc <<EOM
cachedir ~/.tarsnapcache
keyfile ~/tarsnap.key
print-stats
humanize-numbers

exclude .git
exclude *.log
exclude *.zip
exclude *.lock
exclude *.gz
exclude .tarsnapcache
exclude data/web/*/var/*/*
exclude data/web/*/media/catalog/product/cache/*
exclude data/web/*/media/js/*
exclude data/web/*/media/css/*.css
EOM
```

Four, test this setup. The initial upload could take up to several hours.

```bash
# Create a backup archive called "backup-WEEKDAY"
tarsnap -c -f backup-`date "+%A"` -v  ~
```

Five, implement this as a daily cron. To make optimal use of the tarsnap efficiency algorithm, you should NOT use compression on your local database dump and you should dump always to the same file. Lets put this in a tiny script first:

```bash
cat > ~/backup/makebackup.sh <<EOM
TODAY=`date "+%A"`; 
flock -n ~/.mysqldump chronic n98-magerun db:dump --root-dir=~/public --no-interaction --strip @stripped ~/backup/mysql-latest.sql; 
flock -n ~/.tarsnap.lock tarsnap -d backup-\$TODAY 2>/dev/null; 
flock -n ~/.tarsnap.lock chronic tarsnap -c -f backup-\$TODAY ~ 
EOM
chmod 755 ~/backup/*.sh

```

So including the database dump command above, your ```crontab -e``` would become:

```bash
MAILTO=yourmailadress@here
0 2 * * * flock -n ~/.makebackup.lock ~/backup/makebackup.sh 
```

This will create an archive for every day of the week. It will overwrite old archives. 
You will only pay for the unique, compressed amount of data. For an average shop, this is 1/3 of the size. 

### Restoring

List the archives:

```bash 
tarsnap --list-archives --verbose
```

List the contents of a specific archive:

```bash
tarsnap -t -f <archive>
```

Extract a file:

```bash
tarsnap -x -f <archive> <file or files>
tarsnap -x -f backup-Monday data/web/site.com/Nieuwsbrief.png
```

Note: restoring will use the same directory structure. The command above will recreate "data/web/site.com" from the current working dir. So do a ```cd /``` first, to overwrite the old files in the right location.

