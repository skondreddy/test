# Installing Payara Server Instances
**Note: Make sure a Java version greater than 1.8_262 needs to be installed for Payara server to run**
1. Download and extract Payara 5 to *payara5* folder.
2. Make sure the path is set to ensure JDK is first on the PATH variable.
   ```
   cd ../payara5/glassfish/bin
   ```   
3. Login as *trout* user and navigate to the bin directory
4. Now set the passwords for the 2 administration users asadmin and admin (Enter the below commands and follow the instructions, the default password is blank)
   ```
   ./asadmin change-master-password -savemasterpassword
   ./asadmin change-admin-password
   ```
5. Start domain using the following command.
   ```
   ./asadmin start-domain domain1
   ```
6. Once the domain is started run the following command to enable secure admin login.
   ```
   ./asadmin enable-secure-admin
   ```
7. Now login to server admin at port 4848.
8. Add sescoDB data source
9. Change JVM option to -Xmx2048M
10. Deploy the application(s).