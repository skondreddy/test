# sescollc.com website

The sescollc.com website is running in a Amazon Lightsail VM.

## Instances

AWS account id: 4327-7148-3482

Once you are logged in to the AWS account you can search for the lightsail service. There are two instances for Lightsail setup. One for production and one for test. To login to either of these you will need the ssh key. You can download this by clicking on the instance and at the bottom of the page it will have a link to download the ssh key, or it is stored in our Zoho vault (ask Jasen, Shawn, Brett).

- prod
    - 3.18.76.88
    - Sesco_website_Prod_LAMP_PHP_5-512MB-Ohio-1
    - 512 MB RAM, 1 vCPU, 20 GB SSD
    - user: bitnami

- test
    - 3.17.221.124
    - Sesco_website_Test_LAMP_PHP_5-512MB-Ohio-1
    - 512 MB RAM, 1 vCPU, 20 GB SSD
    - user: bitnami

## Domain
The domain name is registered in route 53 in the same AWS account.

## Update page
To update a page you will need to connect to the production instance.

### xml (jobs, news, etc.)
To update jobs on the jobs page you will need to update the xml in htdocs. There is a soft-link in the bitnami user home page. Then you will need to update the xml for the page you need to change.

```
/home/bitnami/htdocs/xml/ -> /opt/bitnami/apache2/htdocs/xml/
```

For example jobs.xml to change the job listings update the jobs.xml file:

```
/home/bitnami/htdocs/xml/jobs.xml -> /opt/bitnami/apache2/htdocs/xml/jobs.xml
```
