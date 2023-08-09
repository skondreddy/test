# Sesco Servers

----------------------
## Database servers

Login with your Sesco user name (i.e. ctrusiak...) and the password is sesco$123.

Server Name | Purpose
----------- | -------
lpgsg102d   | ???
lpgsg201d   | Linux Postgres database
lpgsg301d   | Linux Postgres database
lpgsg302d   | Linux Postgres database
lpgsi303d   | Linux Postgres database
lpgsg304d   | Linux Postgres database
lpgsg305d   | Linux Postgres database

### Commands available via sudo:
* /usr/bin/su - postgres
* /usr/bin/podman
* /usr/bin/systemctl * postgresql-11.service
* /usr/bin/systemctl * postgresql-12.service
* /usr/bin/pganalyze-collector
 
## Job servers

Server Name | Purpose
----------- | -------
ljobg301a   | Primary jobs
bumbleb     | Alternate name for ljobg301a
ljobv302a   | Primary jobs (Monroeville)
ponos       | Alternate name for ljobv302a
lcapi301a   | Off sight backup jobs, and application server; Iron Mountain
jobbak1     | Floating name for backup jobs server

## Application servers

Server Name | Purpose
----------- | -------
aquarium1   | Application server: Payara cluster member (and DAS)
aquarium2   | Application server: Payara cluster member
lpayv303a   | Application server: Payara cluster member (and DAS); ActiveMQ; Monroeville
iris1       | Alternate name for lpayv303a
lpayv304a   | Application server: Payara cluster member; ActiveMQ; Monroeville
iris2       | Alternate name for lpayv304a
merry       | Glassfish web applications and nagios
servbak1    | Floating name for backup application server

## Utility servers

Server Name | Purpose
----------- | -------
energizer   | Jenkins, Trac, Archiva, Jira
ljenv301a   | Build server: Jenkins; Archiva; Monroeville
hera        | Alternate name for ljenv301a
lmonv302a   | Monitoring server: nagios
argus       | alternate name for lmonv302a
