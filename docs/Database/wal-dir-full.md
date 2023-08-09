# pg_wal directory full

If pg_wal fills up `/opt/postgres/pgdata-sesco/data/pg_wal`, the database will crash and go into recovery mode. The WAL directory may fill up because of a failing `archive_command` or a high number of transactions in the DB where the files couldn’t catch up. 

# NEVER MANUALLY DELETE A WAL FILE. EVER. 

Let postgres manage WAL removal. 

Before doing anything copy the existing WAL directory to a safe location

`cp /opt/postgres/pgdata-sesco/data/pg_wal/* /opt/postgres/pgbackup/`

Now find the latest WAL file postgres requires to be present

`/usr/pgsql-11/bin/pg_controldata -D /opt/postgres/pgdata-sesco/data`

You are looking for:
```
Latest checkpoint's REDO WAL file:    000000020000CBDA00000002
```

In this case `000000020000CBDA00000002` is the min file to keep

Run archive cleanup to remove files prior to this file 

`/usr/pgsql-11/bin/pg_archivecleanup /opt/postgres/pgdata-sesco/data/pg_wal 000000020000CBDA00000002`

Old files will safely be removed freeing up space


## Verify archiving is working

`pgbackrest check --stanza=sescoprd --log-level-console=info`

You may see errors like 
```
2022-06-07 00:58:31.635 EDT [48972] LOG:  archive command failed with exit code 55
2022-06-07 00:58:31.635 EDT [48972] DETAIL:  The failed archive command was: pgbackrest --stanza=sescoprd archive-push pg_wal/000000020000CBA300000004
2022-06-07 00:58:31.635 EDT [48972] WARNING:  archiving write-ahead log file "000000020000CBA300000004" failed too many times, will try again later
```

If so, copy the needed WAL files from your backup location `/opt/postgres/pgbackup/` into the current WAL directory for processing. After all the files are finished archiving, make sure the WAL directory is getting automatically cleaned up and old files are removed.

If the requested WAL file has already been removed without being archived, you can temporarily change the archive command to `archive_command = '/bin/true'` and all the files will process. Use this as a last resort because a point in time recovery will now be impossible past this point. Immediately take a full db backup. After the full backup finishes, the database is back to normal.
