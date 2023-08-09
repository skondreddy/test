---
layout: default
title: Running Jobs on a Different Server
nav_order: 2
parent: Monitoring Jobs
---

# Running jobs on a different server

### Common Steps

1. Ensure that all needed files are committed/pushed in **jobs\_bin**, **jobs\_config** and **lib** on the primary jobs server
2.  Update the **jobs\_bin** directory

    ```
    cd jobs_bin
    git pull
    ```
3.  Update the **jobs\_config** directory

    ```
    cd jobs_config
    git pull
    ```
4.  Update the **lib** directory

    ```
    cd lib
    git pull
    ```

### Run cron jobs on a different server

1.  Remove the crontab from the primary server

    ```
    crontab -r
    ```
2.  On the new server, regenerate the crontab files from **crontab.template**

    ```
    mkcron
    ```
3.  Install the crontab with the hostname of the new server as the suffix.

    ```
    crontab crontab.{hostname}
    ```

### Run continuous jobs on a different server

1.  Stop the jobs on the primary server (currently bumbleb)

    ```
    stopSescoJobsBumbleb
    ```
2.  Start the jobs on the new server (_jobbak1_)

    ```
    startSescoJobsJobbak1
    ```
