# Udacity Server Configuration Project
This file outlines the steps needed to configure an Ubuntu server to serve a Flask application with a Python database. These steps are specific to Project 5 of Udacity's Fullstack Web Developer Nanodegree.

## Accessing the Server
IP Address: `52.26.243.255`
SSH Port: `2200`

## Viewing the Web Application
URL: `http://52.26.243.255`

## Steps to Configure Server and Install Software

### Setup User
1. Initially ssh in as root.
2. Create a new user: `adduser <USERNAME>`
3. Add new user to the sudo group: `gpasswd -a <USERNAME> sudo`
4. Add public key to user's account so we can ssh into server as the user
  - in user's home, run: `mkdir .ssh`
  - copy `.ssh/authorized_keys` from root's home to user's home
  - change owner and group of the new authorized_keys file to user
  - change folder permissions of .ssh folder: `chmod 700 .ssh`
  - change file permission on authorized_keys: `chmod 644 authorized_keys`

### Secure Server
1. Edit `/etc/ssh/sshd_config`
  - Disable password based logins by making sure `Password Authentication` is set to no.
  - Disable remote root login by setting `PermitRootLogin` to no.
  - Set ssh `Port` to `2200`
2. Restart ssh: `sudo service ssh restart`
3. Configure firewall
  - By default, deny all incoming requests: `sudo ufw default deny incoming`
  - Allow all outgoing requests: `sudo ufw default allow outgoing`
  - Allow ssh: `sudo ufw allow 2200/tcp`
  - Allow http: `sudo ufw allow 80/tcp`
  - Allow NTP: `sudo ufw allow ntp`
  - Enable firewall: `sudo ufw enable`
  - Check firewall status: `sudo ufw status`

### Install Software
1. First, update currently installed packages:
  - `sudo apt-get update`
  - `sudo apt-get upgrade`
2. Install packages using `sudo apt-get install`:
  - ntp daemon: ntp
  - apache web server: apache2
  - mod-wsgi: libapache2-mod-wsgi
  - PostgreSQL: postgresql
  - Git revision control: git
  - Python package handler: pip
  - Fail2Ban authentication locking: fail2ban
  - Development libraries: libpq-dev python-dev
  - Automatic updates: unattended-upgrades
3. Install Python packages using `sudo pip install`:
  - Flask web framework: Flask
  - http libraries: httplib2
  - Google API: google_api_python_client
  - SQLAlchemy SQL libraries: SQLAlchemy
  - Postgres libraries: psycopg2
  - System Monitoring: Glances

### Configure Server Timezone
1. Run `sudo dpkg-reconfigure tzdata`
2. On the following screens, select `other` and then `UTC`

### Clone and Configure Web Application
1. Navigate to `/var/www`
2. Clone repository from GitHub here:
  - 'sudo git clone <REPOSITORY URL>'
3. Make sure git related files are not accessible from the browser
  - Create an `.htaccess` file in the project root directory
  - add the following line to the file and save: `Redirect 404 /\.git`

### Configure Apache
1. Create an WSGI file named `catalog.wsgi` to act as an interface between Apache and the application. Place it in the application's root directory:

```python
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/<PROJECT_FOLDER>/")

from application import app as application
application.secret_key = '<APP_SECRET_KEY>'
```
2. In the WSGI file above, replace the PROJECT_FOLDER and APP_SECRET_KEY to match the application.
3. Create an Apache config file for the app at `/etc/apache2/sites-available/catalog.conf`

```
<VirtualHost *:80>
                ServerName <SITE_IP_ADDRESS>
                WSGIScriptAlias / /var/www/<PROJECT_FOLDER>/catalog.wsgi
                <Directory /var/www/<PROJECT_FOLDER>/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/<PROJECT_FOLDER>/static
                <Directory /var/www/<PROJECT_FOLDER>/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
4. In the config file above, replace <SITE_IP_ADDRESS> and each instance of <PROJECT_FOLDER> to match the ip address of the app and the folder the application is located in.
5. Restart Apache: `sudo service apache2 restart`

