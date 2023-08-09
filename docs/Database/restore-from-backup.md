# Restore a Backup
Have the sysadmin set up a server with postgres and pgbackrest installed. 

## Configure 
**All DB backups are stored in S3 buckets:**

Coolapp is currently: `https://s3.console.aws.amazon.com/s3/buckets/pgbackrest-repo-coolapp?region=us-east-2&tab=objects`

Dayzer is currently: `https://s3.console.aws.amazon.com/s3/buckets/pgbackrest-repo?region=us-east-2&tab=objects`

 
**Edit the conf file:**

`vi /etc/pgbackrest/pgbackrest.conf` 

The file needs to point to the correct repo for the db you are restoring. Conf files are stored at `https://gitlab.com/sesco/systems/database/postgres-configs/-/tree/master/dbs/pgbackrest`

It will look like:
```
[global]
repo1-type=s3
repo1-path=/repo
repo1-s3-endpoint=s3.amazonaws.com
repo1-s3-bucket=pgbackrest-repo
repo1-s3-key=<redacted>
repo1-s3-key-secret=<redacted>
repo1-s3-region=us-east-2
repo1-retention-full=2
compress-type=lz4
compress-level=6
process-max=18
start-fast=y
backup-standby=y
log-level-console=info

[dayzerprd]
pg1-path=/opt/postgres/pgdata-sesco/data
pg2-host=lpgsg304d
pg2-host-user=postgres
pg2-path=/opt/postgres/pgdata-sesco/data
``` 

## Check configuration

From the linux command line run: `pgbackrest info`

If the configuration is set up correctly you will see the repo with available backups:

```
[postgres@LPGSG305D ~]$ pgbackrest info
stanza: dayzerprd
    status: ok (backup/expire running - 4.10% complete)
    cipher: none

    db (current)
        wal archive min/max (12): 00000001000039D80000009D/000000010000447E00000072

        full backup: 20230107-010008F
            timestamp start/stop: 2023-01-07 01:00:08 / 2023-01-07 15:46:55
            wal start/stop: 00000001000039D80000009D / 00000001000039E500000060
            database size: 9704.8GB, database backup size: 9704.8GB
            repo1: backup set size: 1773.8GB, backup size: 1773.8GB

        diff backup: 20230107-010008F_20230108-010003D
            timestamp start/stop: 2023-01-08 01:00:03 / 2023-01-08 01:26:57
            wal start/stop: 00000001000039E5000000CE / 00000001000039E5000000CE
            database size: 9735GB, database backup size: 241.8GB
            repo1: backup set size: 1778.8GB, backup size: 42.9GB
            backup reference list: 20230107-010008F
```

## Restore
**Run restore command on new server**

`pgbackrest restore --stanza=dayzerprd --log-level-console=info --process-max=18 --delta > restore.log 2>&1 &` 

 

You can use process max to run a parallel restore greatly reducing restore time. If you already have some data in the data directory, delta will only restore changed files. 

 

Before starting postgres set the recovery.conf settings if this is a replica on version 11. 

```
standby_mode = 'on' 
primary_conninfo = 'user=replication passfile=''/var/lib/pgsql/.pgpass'' host=10.190.1.32 port=5432 sslmode=prefer sslcompression=0 krbsrvname=postgres target_session_attrs=any' 
#restore_command = 'scp lpgsg301d:/opt/postgres/pgarchive/%f %p' 
restore_command = 'pgbackrest --stanza=sescoprd archive-get %f %p' 
recovery_target_timeline = 'latest' 
 ```
 
If it is version 12+ make sure a `/opt/postgres/pgdata-sesco/data/standby.signal` file exists before starting the server.

 

## Start postgres 
```
systemctl start postgresql-11.service 
```
 

Possible errors: 
```
2021-11-08 14:40:58.488 EST [2187] LOG:  invalid checkpoint record 

2021-11-08 14:40:58.488 EST [2187] FATAL:  could not locate required checkpoint record 
```
 

Make sure archive-get in the restore command works first!!!!!!!!!!!!!!!!!!!!!!!!!!! 

If all else fails, try resetting the wal 

`/usr/pgsql-12/bin/pg_resetwal -D /opt/postgres/pgdata-sesco/data/ -f`
