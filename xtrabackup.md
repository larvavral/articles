# Xtrabackup

This document describe simple introduction to Percona Xtrabackup.

## 1. What is Xtrabackup

*Percona XtraBackup* is the world’s only open-source, free MySQL hot backup software that performs non-blocking backups for InnoDB and XtraDB databases. With *Percona XtraBackup*, you can achieve the following benefits:

- Backups that complete quickly and reliably
- Uninterrupted transaction processing during backups
- Savings on disk space and network bandwidth
- Automatic backup verification
- Higher uptime due to faster restore time

## 2. Installing Percona Xtrabackup on Ubuntu

Installing Percona XtraBackup from Percona apt repository

- Fetch the repository packages from Percona web:

    ```
    $ wget https://repo.percona.com/apt/percona-release_latest.$(lsb_release -sc)_all.deb
    ```

- Install the downloaded package with dpkg. To do that, run the following commands as root or with sudo:

    ```
    $ sudo dpkg -i percona-release_latest.$(lsb_release -sc)_all.deb
    ```

    Once you install this package the Percona repositories should be added. You can check the repository setup in the `/etc/apt/sources.list.d/percona-release.list` file.

- Remember to update the local cache:

    ```
    $ sudo apt-get update
    ```

- After that you can install the package:

    ```
    $ sudo apt-get install percona-xtrabackup-24
    ```

Note: To uninstall Percona XtraBackup you’ll need to remove all the installed packages.

```
$ sudo apt-get remove percona-xtrabackup-24
```

## 3. Backup database

Before backup, make sure that you have access permissions to mysql folder.

```
$ sudo find /var/lib/mysql -type d -exec chmod 755 {} \;
$ sudo find /var/lib/mysql -type f -exec chmod 755 {} \;
```

Command to backup database using Percona Xtrabackup:

```
$ innobackupex --user=DBUSER --password=SECRET --parallel=4 --use-memory=4G /path/to/backup/dir/
```

Btw, you can edit the parallel and use memory parameter depend on the server machine.

## 4. Restore database 

Before restore, if the memory is enough, you should keep the old data for safety.

```
$ sudo mv /var/lib/mysql /var/lib/mysql_old
```

We can use `innobackupex`

```
$ sudo innobackupex --copy-back /data/backup/
```

Or use `rsync` or `cp` to restore the files. (Test)

```
$ rsync -avrP /data/backup/ /var/lib/mysql/
```

As files’ attributes will be preserved, in most cases you will need to change the files’ ownership to mysql before starting the database server, as they will be owned by the user who created the backup:

```
$ chown -R mysql:mysql /var/lib/mysql
```

Then restart mysql service:

```
$ sudo service mysqld restart
```
