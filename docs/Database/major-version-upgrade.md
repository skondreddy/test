# Upgrade postgres major versions
## Install required packages on all machines
```
sudo yum install postgresql13 postgresql13-contrib postgresql13-devel postgresql13-libs postgresql13-plpython3 postgresql13-server
sudo yum install postgresql14 postgresql14-contrib postgresql14-devel postgresql14-libs postgresql14-plpython3 postgresql14-server
sudo yum install pg_repack_13 pg_repack_14
sudo sudo yum install timescaledb-2-postgresql-14
```

Specific version for upgrade compatibility. Must run loader first as it is a dependency
```
sudo yum install timescaledb-2-loader-postgresql-13-2.3.1-0.el7
sudo yum install timescaledb-2-postgresql-13-2.3.1-0.el7
```


Initialize the new version 13 directory on primary
```
/usr/pgsql-13/bin/initdb -D /opt/postgres/pgdata-sesco/13 --wal-segsize=256
```

Add timescale to preload so checks pass on primary
```
echo "shared_preload_libraries = 'timescaledb'" >> /opt/postgres/pgdata-sesco/13/postgresql.conf
```

Shut down primary, then replicas
```
/usr/pgsql-11/bin/pg_ctl stop -D /opt/postgres/pgdata-sesco/data/ -m fast
```


**IMPORTANT** Make sure replicas are all caught up. Run on primary and replica and verify that the “Latest checkpoint location” values match in all clusters
```
/usr/pgsql-11/bin/pg_controldata -D /opt/postgres/pgdata-sesco/data/
```

Run upgrade command. Remove the --check to actually run
```
/usr/pgsql-13/bin/pg_upgrade --old-bindir /usr/pgsql-11/bin --new-bindir /usr/pgsql-13/bin --old-datadir /opt/postgres/pgdata-sesco/data/ --new-datadir /opt/postgres/pgdata-sesco/13/ --link --check
```

Update the replicas before starting any server. Run rsync on primary to replica. Remove --dry-run to actually run
```
rsync --archive --delete --hard-links --size-only --no-inc-recursive /opt/postgres/pgdata-sesco/data /opt/postgres/pgdata-sesco/13 lpgsg102d:/opt/postgres/pgdata-sesco --dry-run
```

Add postgresql.conf and pg_hba.conf to all the new data directories. Use new stanza name for pgbackrest commands

Add standby.signal to replica database to tell it to be a replica
```
touch /opt/postgres/pgdata-sesco/13/standby.signal
```

Start primary, then replicas
```
/usr/pgsql-13/bin/pg_ctl start -D /opt/postgres/pgdata-sesco/13/
```

Postgres is now at v13. We can update timescale package now on all machines
```
sudo yum update timescaledb-2-postgresql-13
```

Log in to primary in psql and update timescale extension
```
psql -X
\c sescoprd
alter extension timescaledb update;
\dx
\q
```

Unmount the pg_wal directory (sysadmin)


Run delete old cluster on all databases
```
./delete_old_cluster.sh
```


We are now on 13 with updated timescale. Upgrading to 14

Initialize the new version 14 directory on primary.
```
/usr/pgsql-14/bin/initdb -D /opt/postgres/pgdata-sesco/14 --wal-segsize=256
```

Add timescale to preload so checks pass on primary
```
echo "shared_preload_libraries = 'timescaledb'" >> /opt/postgres/pgdata-sesco/14/postgresql.conf
```

Shut down primary, then replicas
```
/usr/pgsql-13/bin/pg_ctl stop -D /opt/postgres/pgdata-sesco/13/ -m fast
```

Make sure replicas are all caught up. Run on primary and replica and verify that the “Latest checkpoint location” values match in all clusters
```
/usr/pgsql-13/bin/pg_controldata -D /opt/postgres/pgdata-sesco/13/
```

Remount pg_wal to /opt/postgres/pgdata-sesco/14/pg_wal. Copy existing data in `/opt/postgres/pgdata-sesco/14/pg_wal` to mount point before switching


Run upgrade command on primary. Remove the --check to actually run
```
/usr/pgsql-14/bin/pg_upgrade --old-bindir /usr/pgsql-13/bin --new-bindir /usr/pgsql-14/bin --old-datadir /opt/postgres/pgdata-sesco/13/ --new-datadir /opt/postgres/pgdata-sesco/14/ --link --check
```

Update the replicas before starting any server. Run rsync on primary to replica. Remove --dry-run to actually run
```
rsync --archive --delete --hard-links --size-only --no-inc-recursive /opt/postgres/pgdata-sesco/13 /opt/postgres/pgdata-sesco/14 lpgsg102d:/opt/postgres/pgdata-sesco --dry-run
```

Add postgresql.conf and pg_hba.conf to all the new data directories. Use new stanza name for pgbackrest commands.


Add standby.signal to replica databases to tell it to be a replica
```
touch /opt/postgres/pgdata-sesco/14/standby.signal
```


Update `/etc/pgbackrest/pgbackrest.conf` on all servers to point to the new data directory and new stanza name


Start primary, then replicas
```
/usr/pgsql-14/bin/pg_ctl start -D /opt/postgres/pgdata-sesco/14/
```


Make a new stanza so we have clean backups
``
pgbackrest stanza-create --stanza=sescoprd-14 --log-level-console=info
``


Delete old cluster
```
./delete_old_cluster.sh
```

Start a new backup on the replica db. Check cron for any script paths that need updated.
