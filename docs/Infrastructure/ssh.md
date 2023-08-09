# SSH key pair authentication

## General methodology
The key pair authentication described here assigns one key to a
username/role. The same username/role can/will be present on different hosts.
The public keys listed here should be added to the *~/.ssh/authorized_keys*
file on each user/host combo.

Username     | Role                      | Key              |
--------     | ----                      | ---              |
sescojobs    | Java cron and daemon jobs | sescojobs-rsa    |
trout        | Glassfish app server      | trout-rsa        |
sescobuilder | Java build artifacts      | sescobuilder-rsa |
nagios       | Nagios monitoring system  | nagios-rsa       |

## Common steps
These steps are prerequisites for any of the formulas below.

1. If you do not have an ~/.ssh directory create one
   ```
   mkdir .ssh
   ```
2. Make sure the permissions for ~/.ssh are drwx------
   ```
   chmod 700 .ssh
   ```
3. Change directory to your ~/.ssh directory
   ```
   cd ~/.ssh
   ```
4. Create ~/.ssh/config with permissions -rw-r--r--
   ```
   touch config
   chmod 644 config
   ```
## Use SSH key pair authentication for servers

### Use SSH key pair authentication for the user __sescojobs__ from a Mac
1. Copy the key pair sescojobs-rsa and sescojobs-rsa.pub from the .ssh sub directory of the sescojobs home directory on the primary jobs server.
   to ~/.ssh using commands:
   ```bash
   scp sescojobs@ljobv302a.sesco.com:.ssh/sescojobs-rsa ./sescojobs-rsa
   password: S3sC0j0bs
   scp sescojobs@ljobv302a.sesco.com:.ssh/sescojobs-rsa.pub ./sescojobs-rsa.pub
   password: S3sC0j0bs
   
   ```
2. Ensure that the permissions on sescojobs-rsa are -rw-------
   ```
   chmod 600 sescjobs-rsa
   ```
3. Ensure that the permissions on sescojobs-rsa.pub are -rw-r--r--
   ```
   chmod 644 sescojobs-rsa.pub
   ```
4. Edit .ssh/config to add configurations for sescojobs: 
   ```
   # sescojobs @ ponos/LJOBG301A primary jobs server - sescojobs/S3sC0j0bs
   Host ljobv302A.sesco.com
       IdentityFile ~/.ssh/sescojobs-rsa
    
   # sescojobs @ jobbak1 secondary jobs server floating name
   Host jobbak1.sesco.com
       IdentityFile ~/.ssh/sescojobs-rsa

   # sescojobs @ lcapi301a current secondary jobs server (iron mountain)
   Host lcapi301a.sesco.com
       IdentityFile ~/.ssh/sescojobs-rsa
   ```

### Use SSH key pair authentication to login as the user __trout__ from a Mac
1. Copy the key pair trout-rsa and trout-rsa.pub from the .ssh sub directory of the trout home directory on the primary Payara server.
   to ~/.ssh using commands:
   ```bash
   scp trout@lpayv303a.sesco.com:.ssh/trout-rsa ./trout-rsa
   password: tr0ut1
   scp trout@lpayv303a.sesco.com:.ssh/trout-rsa.pub ./trout-rsa.pub
   password: tr0ut1
   ```
2. Ensure that the permissions on trout-rsa are -rw-------
   ```
   chmod 600 trout-rsa
   ```
3. Ensure that the permissions on trout-rsa.pub are -rw-r--r--
   ```
   chmod 644 trout-rsa.pub
   ```
4. Edit ~/.ssh/config to add configurations for trout:
   ```
   # iris1/lpayv303a.sesco.com primary payara cluster server - trout/tr0ut1
   Host lpayv303a.sesco.com
       IdentityFile ~/.ssh/trout-rsa

   # iris2/lpayv304a.sesco.com primary payara cluster server - trout/tr0ut1
   Host lpayv304a.sesco.com
       IdentityFile ~/.ssh/trout-rsa

   # servbak1/lcapi301a.sesco.com backup payara cluster server - trout/tr0ut1
   Host lcapi301a.sesco.com
       IdentityFile ~/.ssh/trout-rsa
   ```

### Use SSH key pair authentication for nagios servers from a Mac
1. Copy the key pair nagios-rsa and nagios-rsa.pub from nagios@lmonv302ia.sesco.com
   to ~/.ssh using commands:
   ```bash
   scp nagios@lmonv302a.sesco.com:.ssh/nagios-rsa ./nagios-rsa
   password: M0nit0r
   scp nagios@lmonv302a.sesco.com:.ssh/nagios-rsa.pub ./nagios-rsa.pub
   password: M0nit0r
   ```
2. Ensure that the permissions on nagios-rsa are -rw-------
   ```
   chmod 600 nagios-rsa
   ```
3. Ensure that the permissions on nagios-rsa.pub are -rw-r--r--
   ```
   chmod 644 nagios-rsa.pub
   ```
4. Edit ~/.ssh/config to add configurations for nagios:
   ```
   # argus/lmonv302a.sesco.com nagios server nagios/M0nit0r
   Host lmonv302a.sesco.com
       IdentityFile ~/.ssh/nagios-rsa
   ```

### Use SSH key pair authentication for sescobuilder servers from a Mac
1. Copy the key pair sescobuilder-rsa and sescobuilder-rsa.pub from sescobuilder@ljenv301a.sesco.com
   to ~/.ssh using commands:
   ```bash
   scp sescobuilder@ljenv301a.sesco.com:.ssh/sescobuilder-rsa ./sescobuilder-rsa
   password: Bu1ld3R
   scp sescobuilder@ljenv301a.sesco.com:.ssh/sescobuilder-rsa.pub .sescobuilder-rsa.pub
   password: Bu1ld3R
   ```
2. Ensure that the permissions on sescobuilder-rsa are -rw-------
   ```
   chmod 600 sescobuilder-rsa
   ```
3. Ensure that the permissions on sescobuilder-rsa.pub are -rw-r--r--
   ```
   chmod 644 sescobuilder-rsa.pub
   ```
4. Edit ~/.ssh/config to add configurations for sescobuilder:
   ```
   # hera/ljenv301a.sesco.com build server
   Host ljenv301a.sesco.com
       IdentityFile ~/.ssh/sescobuilder-rsa
   ```
