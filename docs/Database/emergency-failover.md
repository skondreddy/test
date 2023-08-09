# Emergency Failover

## Switch primary DB 

Let's assume lpgsg301d is primary and is having issues and we need to fail over to lpgsg302d 

 
Change `postgres.sesco.com` DNS entry to `10.190.1.33` (IP address of new primary) (Jasen or Sysadmin) 

 
Log in to new primary as the postgres user and run: 

`pg_ctl promote -D /opt/postgres/pgdata-sesco/data`


Verify by running `select pg_is_in_recovery();` from psql or a dbeaver query window in the new primary. Should be "f" for false. This server is now primary. 

 
## Switch Replica Databases
Change `postgres-ro.sesco.com` DNS entry to `10.190.2.11` (a remaining replica) 

 
Update the `/opt/postgres/pgdata-sesco/data/recovery.conf` in remaining replicas to stream from the new primary now. Change host IP in the line below to be the new primary
```
primary_conninfo = 'user=replication passfile=''/var/lib/pgsql/.pgpass'' host=10.190.1.32 port=5432 sslmode=prefer sslcompression=0 krbsrvname=postgres target_session_attrs=any' 
```
 
Restart the replicas. They will now replicate from the new primary 
`sudo systemctl restart postgresql-11.service` 
 

The crontab on the primary is different from replicas. It has daily analyzes and some monthly jobs. Copy the home directory contents and crontab of the `postgres` user to the new primary when failing over and disable the old crontab for postgres user (sysadmin). 

 

## pgbackrest 

The configuration on changed databases need to be updated

`vi /etc/pgbackrest/pgbackrest.conf`


Use the configurations https://gitlab.com/sesco/systems/database/postgres-configs/-/tree/master/shared/pgbackrest for any databases where roles changed

When failing over, to be safe I made a new pgbackrest repo for the new timeline 

 
```
pgbackrest stanza-create --stanza=sescoprd-newstanza --log-level-console=info 
```

Running `pgbackrest info` in linux will check the configuration. Check that the archive status is updating with new files `pgbackrest check --stanza=sescoprd --log-level-console=info`

Interesting article https://www.sqlpac.com/en/documents/postgresql-switch-failover-failback-standby-databases.html 

 
