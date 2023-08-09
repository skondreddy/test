## Stop Service: 

`sudo systemctl stop postgresql-11.service` 

 

## Start Service: 

`sudo systemctl start postgresql-11.service` 

## Stop manually graceful: 

`/usr/pgsql-11/bin/pg_ctl stop -D /opt/postgres/pgdata-sesco/data -m fast` 

 


## Stop manually less graceful: 

`/usr/pgsql-11/bin/pg_ctl stop -D /opt/postgres/pgdata-sesco/data -m immediate` 

 

## Start manually: 

`/usr/pgsql-11/bin/pg_ctl start -D /opt/postgres/pgdata-sesco/data` 

 

 

