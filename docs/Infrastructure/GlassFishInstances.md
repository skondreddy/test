# PAYARA SERVER Instances

The Production PAYARA Server is on Aquarium1 and Aquarium2.  Login as trout user.
	Aquarium1 is the Domain Admin Server using domain sesco-prod.
    cd /app/payara5/glassfish/bin
    run ./asadmin start-domain sesco-prod
	
	Both servers have a Clustered Node.  These nodes can be started from Aquarium1
    cd /app/payara5/glassfish/bin
    run ./asadmin --passwordfile=/app/payara5/glassfish/domains/sesco-prod/config/passwords.txt start-instance Prod_NodeX (Where X is a Node number matching the machine)
	
	If  a big change was made to configurations, need to Sync these by running
    run ./asadmin --passwordfile=/app/payara5/glassfish/domains/sesco-prod/config/passwords.txt start-instance --sync full Prod_NodeX (Where X is a Node number matching the machine)
	
	If DAS has a tendency to lose communincation with the Nodes.  To stop them log on to each server and do
	ps-eaf|grep java|grep Node   kill -9 alll pids
	
	The admin screen is at https://aquarium1:4848/  Login as admin user
	
	To pull a new ear on aquarium1 goto /home/trout/bin and run command ./getLatestEar
		Bounce both nodes as above to establish communication with the DAS if lost (Click on "Clusters" to see if Node show as Green).  Login to the Admin screen select Applications from left menu select redeploy and follow the screen prompts.
	


	 

	
	
	

