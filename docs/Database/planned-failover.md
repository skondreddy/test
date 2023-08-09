# Postgres Planned Failover and Fail-back 

## Servers 

Currently, we have 3 postgres servers/databases running in our PRD tier: 
```
lpgsg301d 
lpgsg302d 
lpgsi303d  
```
The servers are identical and can switch roles at any time as needed. Here are the roles of servers at the time of writing: 

lpgsg302d is the primary 

lpgsg301d is a replica streaming from lpgsg302d 

lpgsi303d is a replica streaming from lpgsg302d hosted at Iron Mountain 

## Objective 

lpgsg302d needs to go down for maintenance. lpgsi303d will become the new primary and lpgsg301d will become the first replica. 


## Shift Replication Sources to Future Primary 

lpgsg302d is currently streaming from lpgsg302d. lpgsg302d is going down and lpgsg301d will need to replicate from the future primary (lpgsi303d). 

On lpgsg302d, update `/opt/postgres/pgdata-sesco/data/recovery.conf` to point to the future primary. 
```
standby_mode = 'on'
primary_conninfo = 'user=replication passfile=''/var/lib/pgsql/.pgpass'' host=10.190.1.32 port=5432 sslmode=prefer sslcompression=0 krbsrvname=postgres target_session_attrs=any'
restore_command = 'pgbackrest --stanza=sescoprd archive-get %f %p'
recovery_target_timeline = 'latest'
```
Restart the database 
```
systemctl stop postgresql-11.service  
systemctl start postgresql-11.service  
systemctl status postgresql-11.service  
```
Log into the future primary server and check that the replica is now switched over. 

`select * from pg_stat_replication;`

Check the log file of the replica to make sure there are no errors. 

`tail /opt/postgres/pglog/postgresql-Thu.log`

We now have cascading replication in the order of lpgsg302d -> lpgsi303d -> lpgsg301d. When lpgsg302d is taken down and lpgsi303d is promoted to primary, we will already have a replica database (lpgsg301d) automatically replicating from the new primary. 

## Fail Over 

Stop the jobs and anything else that will try to connect to the primary server (postgres.sesco.com). 

Stop the current primary db. 
```
systemctl stop postgresql-11.service  
systemctl status postgresql-11.service  
```
The old primary must be shut down cleanly before any promotion can occur. Verify in the primary log file. 

`tail /opt/postgres/pglog/postgresql-Thu.log`  

You should see `LOG: database system is shut down.` 

Check the log files of the replicas to make sure there are no errors. 

`tail /opt/postgres/pglog/postgresql-Thu.log`  

Errors on history files like `00000009.history` can be ignored if the timeline id hasn't happened yet. 

If there are no errors, maintenance can now start on the current primary server. 

On the future primary, promote it to be the new primary server. 

`pg_ctl promote -D /opt/postgres/pgdata-sesco/data/`  

Successful promotion will return: 
```
waiting for server to promote....... done 
server promoted  
```
Verify in the now primary log file: 

`tail /opt/postgres/pglog/postgresql-Thu.log`  
```
LOG:  received promote request 
LOG:  selected new timeline ID: 6 
LOG:  archive recovery complete 
LOG:  database system is ready to accept connections  
```
The former first standby server is now the primary server. 

Check the replica db to see if it is still in sync. 

`select now()-pg_last_xact_replay_timestamp() as replication_lag;`  


Update the DNS entry `postgres.sesco.com` to point to the new primary. Update the DNS entry `postgres-ro.sesco.com` to point to the new first replica. 

Once DNS has been updated and verified, jobs and applications can be restarted and connect. 

On the former primary (server maintenance is being performed on), add a /opt/postgres/pgdata-sesco/data/recovery.conf file. This will tell the database to be a replica and connect to the new primary once it is rebooted. 
```
standby_mode = 'on'
primary_conninfo = 'user=replication passfile=''/var/lib/pgsql/.pgpass'' host=10.190.1.32 port=5432 sslmode=prefer sslcompression=0 krbsrvname=postgres target_session_attrs=any'
restore_command = 'pgbackrest --stanza=sescoprd archive-get %f %p'
recovery_target_timeline = 'latest'
```
When maintenance is complete reboot the server and verify postgres started. 
```
systemctl status postgresql-11.service  
tail /opt/postgres/pglog/postgresql-Thu.log  

LOG:  entering standby mode 
LOG:  database system is ready to accept read only connections 
LOG:  started streaming WAL from primary at A2/0 on timeline 8  
```
On the new primary, check to see if all the replicas are connected and streaming. 
```
SELECT *  
FROM pg_stat_replication;  
```
If for some reason writes happened to the former primary after the promotion took place, you will need to rewind the former primary before connecting to the new primary. 

`pg_rewind --target-pgdata=/opt/postgres/pgdata-sesco/data --source-server="port=5432 user=postgres dbname=sescoprd host=lpgsi303d password=postgres" --progress`  

## PGBackrest
Update the pgbackrest conf files depending on if the database is primary or replica.
`/etc/pgbackrest/pgbackrest.conf`
`https://gitlab.com/sesco/systems/database/postgres-configs/-/tree/master/dbs/pgbackrest`

## Crontabs 

The crontab on the primary is different from replicas. It has daily analyzes and some monthly jobs. Copy the scripts and crontab to the new primary when failing over and disable the old crontab. 

## Fail Back 

To fail back, repeat the fail over steps with the servers in new positions. Be aware the file recovery.conf is renamed to recovery.done by pg_ctl promote. If a recovery.conf file exists, the database will try to connect as a replica to the primary defined in the connection string. If no recovery.conf exists, the database will start as a primary. 

## Notes 

We DO NOT want to use Physical replication slots/PRIMARY_SLOT_NAME shown with SELECT * FROM pg_replication_slots;. Physical slots will prevent wal files from being moved out of pg_wal if a replica falls behind. This is bad because it could crash the primary by running out of space. Because we are archiving for 3 days, a replica trying to rejoin the primary can just retrieve needed wal files from the archive with the restore_command in the recovery.conf file up to 3 days later. 

For another example see: https://www.sqlpac.com/en/documents/postgresql-switch-failover-failback-standby-databases.html 

