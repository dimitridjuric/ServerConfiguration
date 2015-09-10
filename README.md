### Full Stack Nanodegree
# Project 5 - Linux Server Configuration

### Details
- IP adress: 52.27.171.136
- SSH port: 2200
- Application URL: http://ec2-52-27-171-136.us-west-2.compute.amazonaws.com/

### Configuration
##### SSH
The server is configured to not accept SSH root login and can only use key based authentication.  All these settings are made in the file `/etc/ssh/sshd_conf`.

I've created two new users, grader and catalog, both can login with SSH and can sudo

##### Firewall - UFW
The firewall only accepts SSH from port 2200. I've installed Fail2Ban to monitor repeat attempts to login. It is configured for a maximum of 3 attempts and a ban time of 10 minutes. Fail2ban is also configured to monitor for errors on the apache error log. The configuration file is `/etc/fail2ban/jail.local`

##### Security updates
I've installed Unattended Upgrades to automatically update the OS security. It is configured in the file `/etc/apt/apt.conf.d/50unattended-upgrades`. It only installs security updates.

##### Mail Transfer Agent
I've installed Mailutils to configure Postfix. The server can send email to the administrator. Fail2Ban is configured to send email when an intrusion attempt is detected and Unatended Updates sends an email about the packages updates.

##### Logwatch
I've also installed Logwatch, a program that filters the logs and sends an email a day in a human readable format. the settings file is `/usr/share/logwatch/default.conf/logwatch.conf`

##### Postgres
I've installed Postgres and created a new postgres user named catalog, and created a new database named catalog. The `/etc/postgresql/9.3/main/pg_hba.conf` file only allows local connections to the database.

### Application Deployment

The application was cloned from my catalog app git repository. I have copied it to `/var/www/gallery-inventory`. There is a bash script I used for the project 3, `pg_config.sh` that can be used to install all the necessary python packages, install postgres, create the catalog user, setup the database relations and load the initial database.

The python packages are: psycopg2, flask 0.9, sqlalchemy, pip, werkzeug 0.8.3, request, bleach, oauth2client, flask-seasurf.
We could use a virtual environment to keep the specific versions of flask and werkzeug from being updated but since there is only one application running on this server I left this as a global installation.

I've created a wsgi file in the `/var/www/gallery-inventory` folder and a vitual host configuration in `/etc/apache2/sites-available`. In the latter I've added a ServerAlias with the Url of the app http://ec2-52-27-171-136.us-west-2.compute.amazonaws.com/ that I found with a reverse DNS lookup service. To make apache2 use the catalog account, I've added the lines `WSGIDaemonProcess catalog` and `WSGIProcessGroup catalog`. 

I've disabled index browsing using the `a2dismod autoindex` command.

