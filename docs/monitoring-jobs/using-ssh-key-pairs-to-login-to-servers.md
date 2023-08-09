---
layout: default
title: Using SSH Key Pairs to Access Servers
nav_order: 4
parent: Monitoring Jobs
---

# Using SSH key pairs to login to servers

## SSH key pair authentication

### General methodology

The key pair authentication described here assigns one key to a username/role. The same username/role can/will be present on different hosts. The public keys listed here should be added to the _\~/.ssh/authorized\_keys_ file on each user/host combo.

| Username     | Role                      | Key              |
| ------------ | ------------------------- | ---------------- |
| sescojobs    | Java cron and daemon jobs | sescojobs-rsa    |
| trout        | Glassfish app server      | trout-rsa        |
| sescobuilder | Java build artifacts      | sescobuilder-rsa |
| nagios       | Nagios monitoring system  | nagios-rsa       |

### Common steps

These steps are prerequisites for any of the formulas below.

1.  If you do not have an \~/.ssh directory create one

    ```
    mkdir .ssh
    ```
2.  Make sure the permissions for \~/.ssh are drwx------

    ```
    chmod 700 .ssh
    ```
3.  Change directory to your \~/.ssh directory

    ```
    cd ~/.ssh
    ```

#### Common steps

These steps are prerequisites for any of the formulas below.

1.  If you do not have an \~/.ssh directory create one

    ```
    mkdir .ssh
    ```
2.  Make sure the permissions for \~/.ssh are drwx------

    ```
    chmod 700 .ssh
    ```
3.  Change directory to your \~/.ssh directory

    ```
    cd ~/.ssh
    ```
4.  Create \~/.ssh/config with permissions -rw-r--r--

    ```
    touch config
    chmod 644 config
    ```

### Use SSH key pair authentication for job servers

#### Use SSH key pair authentication for job servers from a Mac

1.  Copy the key pair id\_rsa and id\_rsa.pub from sescojobs@lcapi301a to \~/.ssh using commands:

    ```bash
    scp sescojobs@lcapi301a:.ssh/id_rsa ./sescojobs-rsa
    password: S3sC0j0bs
    scp sescojobs@lcapi301a:.ssh/id_rsa.pub ./sescojobs-rsa.pub
    password: S3sC0j0bs
    ```
2.  Ensure that the permissions on sescojobs-rsa are -rw-------

    ```
    chmod 600 sescjobs-rsa
    ```
3.  Ensure that the permissions on sescojobs-rsa.pub are -rw-r--r--

    ```
    chmod 644 sescojobs-rsa.pub
    ```
4.  Edit .ssh/config to add configurations for sescojobs:

    ```
    # sescojobs @ bumbleb/LJOBG301A primary jobs server - sescojobs/S3sC0j0bs
    Host LJOBG301A.sesco.com
        IdentityFile ~/.ssh/sescojobs-rsa
     
    # sescojobs @ jobbak1 secondary jobs server floating name
    Host jobbak1.sesco.com
        IdentityFile ~/.ssh/sescojobs-rsa

    # sescojobs @ lcapi301a current secondary jobs server (iron mountain)
    Host lcapi301a.sesco.com
        IdentityFile ~/.ssh/sescojobs-rsa
    ```

#### Use SSH key pair authentication for glassfish servers from a Mac

1.  Copy the key pair id\_rsa and id\_rsa.pub from trout@lcapi301a to \~/.ssh using commands:

    ```bash
    scp trout@lcapi301a:.ssh/id_rsa ./trout-rsa
    password: tr0ut1
    scp trout@lcapi301a:.ssh/id_rsa.pub ./trout-rsa.pub
    password: tr0ut1
    ```
2.  Ensure that the permissions on trout-rsa are -rw-------

    ```
    chmod 600 trout-rsa
    ```
3.  Ensure that the permissions on trout-rsa.pub are -rw-r--r--

    ```
    chmod 644 trout-rsa.pub
    ```
4.  Edit \~/.ssh/config to add configurations for trout:

    ```
    # Obiwan1 primary glassfish cluster server
    Host obiwan1.sesco.com
        KexAlgorithms +diffie-hellman-group1-sha1
        IdentityFile ~/.ssh/trout-rsa

    # Obiwan2 primary glassfish cluster server - password tr0ut1
    Host obiwan2.sesco.com
        KexAlgorithms +diffie-hellman-group1-sha1
        IdentityFile ~/.ssh/trout-rsa

    # Obiwan3 primary glassfish cluster server - password tr0ut1
    Host obiwan3.sesco.com
        KexAlgorithms +diffie-hellman-group1-sha1
        IdentityFile ~/.ssh/trout-rsa

    # Merry glassfish applications server
    Host merry.sesco.com
        KexAlgorithms +diffie-hellman-group1-sha1
        IdentityFile ~/.ssh/trout-rsa
    ```

#### Use SSH key pair authentication for nagios servers from a Mac

1.  Copy the key pair id\_rsa and id\_rsa.pub from nagios@merry.sesco.com to \~/.ssh using commands:

    ```bash
    scp nagios@merry.sesco.com:.ssh/id_rsa ./nagios-rsa
    password: M0nit0r
    scp nagios@merry.sesco.com:.ssh/id_rsa.pub ./nagios-rsa.pub
    password: M0nit0r
    ```
2.  Ensure that the permissions on nagios-rsa are -rw-------

    ```
    chmod 600 nagios-rsa
    ```
3.  Ensure that the permissions on nagios-rsa.pub are -rw-r--r--

    ```
    chmod 644 nagios-rsa.pub
    ```
4.  Edit \~/.ssh/config to add configurations for nagios:

    ```
    # Merry glassfish applications and nagios server nagios password: M0nit0r
    Host merry.sesco.com
        KexAlgorithms +diffie-hellman-group1-sha1
        IdentityFile ~/.ssh/trout-rsa
        IdentityFile ~/.ssh/nagios-rsa
    ```

#### Use SSH key pair authentication for sescobuilder servers from a Mac

1.  Copy the key pair id\_rsa and id\_rsa.pub from sescobuilder@energizer.sesco.com to \~/.ssh using commands:

    ```bash
    scp sescobuilderi@energizer.sesco.com:.ssh/id_rsa ./sescobuilder-rsa
    password: Bu1ld3r
    scp sescobuilder@energizer.sesco.com:.ssh/id_rsa.pub .sescobuilder-rsa.pub
    password: Bu1ld3r
    ```
2.  Ensure that the permissions on sescobuilder-rsa are -rw-------

    ```
    chmod 600 sescobuilder-rsa
    ```
3.  Ensure that the permissions on sescobuilder-rsa.pub are -rw-r--r--

    ```
    chmod 644 sescobuilder-rsa.pub
    ```
4.  Edit \~/.ssh/config to add configurations for sescobuilder:

    ```
    # Energizer build server
    Host energizer.sesco.com
        IdentityFile ~/.ssh/sescobuilder-rsa
    ```
