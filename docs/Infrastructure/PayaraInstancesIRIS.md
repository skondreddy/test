# PAYARA SERVER Instances on IRIS'

The following payara server domains are created on iris1
- Keystone
- NIServer
- smsService
The following payara server domains are created on iris2
- DayzerReport
- FTRAnalysis
- FTRTrading

## Creating a domain for each of the above
### DayzerReports
First to create a domain run the following command, since we will be creating multiple domains, we need to mention the port number

`./asadmin create-domain --portbase=10080 dayzerreports`

Once the domain is created, start the domain using the following command

`./asadmin start-domain dayzerreports`

Once the domain is started, now we change the admin password and then we enable the secure admin so that we can login to the UI for the domain
Note: The admin password is going to be tr0ut1 and user is admin

`./asadmin --port 10128 change-admin-password` -> Change admin password

`./asadmin --port 10128 enable-secure-admin` -> Enable Secure admin

Once that is done, stop the domain and then restart the domain

`./asadmin stop-domain dayzerreports`

`./asadmin start-domain dayzerreports`

Once the domain is up and running, we need to go change the JVM options

After the JVM options are copied, we have to copy all the jars under the domain to the new domain

Now create a JDBC Connection pool 

    PoolName: sescodb

    Additional Properties
    URL: jdbc:postgresql://postgres.sesco.com/sescoprd?stringtype=unspecified
    password: sescouser
    databaseName: sescoprd
    defaultRowPrefetch: 10000
    serverName: postgres.sesco.com
    datasourceName: sescodb
    user: sescouser
    networkProtocol: tcp
    port: 5432

Now create a JMS Connection Factory. Once the JMS Connection factory is added, add the topics from the Glassfish instance    

Once this is done, we need to deploy the respective war/ear
DayzerReports.war

`cd /home/trout/bin`

`run /getLatest DayzerReports.war`

goto /home/trout/jars and ensure the newest war is renamed without the date in the name

Now deploy the application from the UI.

The same process has to be repeated for all the above services.

Instances moved to Payara (Create commands)

 `./asadmin create-domain --portbase=9080 ftranalysis`

 `./asadmin create-domain --portbase=10080 dayzerreports`

 `./asadmin create-domain --portbase=11080 ftrtrading`

 `./asadmin create-domain --portbase=12080 niserver`

 `./asadmin create-domain --portbase=13080 keystone`

 `./asadmin create-domain --portbase=14080 SMSWeb'



### Admin Screens

[Dayzer Reports](https://iris2:10128/common/index.jsf) User admin 

[FTRAnalysis](https://iris2:9128/common/index.jsf) User admin 

[FTRTrading](https://iris2:11128/common/index.jsf) User admin 

[NIServer](https://iris1:12128/common/index.jsf) User admin 

[Keystone](https://iris1:13128/common/index.jsf) User admin 

[SMSWeb](https://iris1:14128/common/index.jsf) User admin 


### Applications

[Dayzer Reports](http://iris2:10160/DayzerReports)

[FTRAnalysis](http://iris2:9160/FTRAnalysis/FTRAnalysisApp.html)

[FTRTrading](http://iris2:11160/FTRTrading/FTRTradingApp.html)

[NIServer](http://iris1:12160/NIServer)

[Keystone](http://iris1:13160/Keystone/KeystoneMain.html)

[SMSWeb](http://iris1:14160/smsService/index.jsp)


#### NAGIOS Note
The scripts statusViaHttp.pl and statusViaHttp2.pl that point to SMSWeb are on Merry in the /usr/local/nagios/libexec directory
