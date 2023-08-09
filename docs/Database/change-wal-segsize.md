# Changing wal-segsize with 2 databases 

## Stop primary db
On `lpgsg304d` run `sudo systemctl stop postgresql-12.service`. Make sure shutdown was clean without any fatal errors or suspicious warnings.
```
tail -f /opt/postgres/pglog/postgresql-Mon.log
2023-01-30 18:05:55.890 EST [4246] LOG:  received smart shutdown request
2023-01-30 18:05:56.108 EST [4266] LOG:  shutting down
2023-01-30 18:05:57.469 EST [4246] LOG:  database system is shut down
```

## Patch primary db
Let Jasen do his thing


## Reset the WAL on primary db
After patching and reboots are done, reset the WAL
`/usr/pgsql-12/bin/pg_resetwal -D /opt/postgres/pgdata-sesco/data --wal-segsize=256`

Successful completion will show
```
Write-ahead log reset
```

## Update configurations on primary
Pick a new stanza name like `dayzerprd-12`

Change db parameters to use the new stanza name `vi /opt/postgres/pgdata-sesco/data/postgresql.conf`
```
archive_command = 'pgbackrest --stanza=dayzerprd-12 archive-push %p'
restore_command = 'pgbackrest --stanza=dayzerprd-12 archive-get %f %p'
```

Change pgbackrest.conf and replace the stanza name `vi /etc/pgbackrest/pgbackrest.conf`
```
[dayzerprd-12]
```

## Start primary db
On `lpgsg304d` run `sudo systemctl start postgresql-12.service`


## Update DNS
Make sure `dayzerdb-ro.sesco.com` points to primary db `lpgsg304d`. The replica will need rebuilt so queries can only go to the primary.


## Stop replica db
On `lpgsg305d` run `sudo systemctl stop postgresql-12.service`


## Create new stanza on primary db
On the linux command line create the new stanza
```
pgbackrest stanza-create --stanza=dayzerprd-12 --log-level-console=info
```


Run `pgbackrest info`. You should see the old and new stanzas.
```
-bash-4.2$ pgbackrest info
stanza: dayzerdev
    status: ok
    cipher: none

    db (current)
        wal archive min/max (12): 000000010000000000000001/000000010000000000000008

        full backup: 20230130-121422F
            timestamp start/stop: 2023-01-30 12:14:22 / 2023-01-30 12:20:17
            wal start/stop: 000000010000000000000005 / 000000010000000000000005
            database size: 85.9MB, database backup size: 85.9MB
            repo1: backup set size: 9.4MB, backup size: 9.4MB

stanza: dayzerdev-12
    status: error (no valid backups)
    cipher: none

    db (current)
        wal archive min/max (12): none present
```


## Start a new backup
The replica db needs rebuilt, so start a backup on the primary db.
```
pgbackrest backup --type=full --stanza=dayzerprd-12 --process-max=16 --log-level-console=info > backup.log 2>&1 &
```


## Update replica db config
Change pgbackrest.conf and replace the stanza name `vi /etc/pgbackrest/pgbackrest.conf`
```
[dayzerprd-12]
```

## Wait for backup to finish
Wait

## Rebuild replica db
On `lpgsg305d` run:
```
pgbackrest restore --delta --stanza=dayzerprd-12  --process-max=16 --log-level-console=info > restore.log 2>&1 &
```

## Configure replica db
Add signal file to tell the db to start as a replica
```
touch /opt/postgres/pgdata-sesco/data/standby.signal
```

## Start replica db
`sudo systemctl start postgresql-12.service`

## Update DNS
Switch `dayzerdb-ro.sesco.com` back to `lpgsg305d`g305d`
