# Linux-Server-Configuration
I will take a baseline installation of a Linux distribution on a virtual machine and prepare it to host web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

IP: 207.154.237.89
SSH port: 22
User: grader

Step-by-step:

1) Update all currently installed packages
sudo apt-get update
sudo apt-get upgrade

2) Change the SSH port from 22 to 2200.
sudo nano /etc/ssh/sshd_config
- Uncomment and change line with Port 22 to 2200
sudo service ssh restart

3) Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow http
sudo ufw allow ntp
sudo ufw enable

4) Create a new user account named grader
sudo adduser grader

5) Give grader the permission to sudo
sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/grader
sudo nano /etc/sudoers.d/grader
- Change the name of the user in the file for grader.

6) Create an SSH key pair for grader using the ssh-keygen tool.
(on client terminal)
ssh-keygen
- Give the name for the file in wich to save the key
- Enter passphrase for the key
(on server with grant user loged in)
mkdir .ssh
touch .ssh/authorized_keys
(on cliente terminal)
cat .ssh/FKeyProject.pub
(copy the content of the key)
(on server with grant user loged in) 
sudo nano .ssh/authorized_keys
(... paste the content of the key and save)
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
(on client terminal to test de login via key)
ssh grader@207.154.237.89 -p 2200 -i /c/KEYS/FKeyProject

7) Forcing Key Based Authentication
sudo nano /etc/ssh/sshd_config
(set the line start with PasswordAuthentication to no)
PasswordAuthentication no
sudo service ssh restart

8) Configure the local timezone to UTC.
sudo timedatectl set-timezone UTC

8) Install Apache Server Service
sudo apt-get install apache2

9) Install and Enable mod_wsgi
sudo apt-get install libapache2-mod-wsgi python-dev

10) Enable mod_wsgi
sudo a2enmod wsgi

11) Clone Git repositorie
sudo git clone https://github.com/KarluvKing/Item-Catalog.git

12) Install Flask, SQL Alchemy, oauth2client, requests
sudo apt-get install python-pip
sudo pip install Flask
sudo pip install sqlalchemy
sudo pip install oauth2client
sudo pip install requests

13) Configure and Enable a New Virtual Host
sudo nano /etc/apache2/sites-available/Item-Catalog.conf

(Add the following lines of code to the file to configure the virtual host.)

<VirtualHost *:80>
		ServerName localhost
		ServerAdmin email@ruipaivabranco.com
		WSGIScriptAlias / /var/www/Item-Catalog/project.wsgi
		<Directory /var/www/Item-Catalog/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/Item-Catalog/static
		<Directory /var/www/Item-Catalog/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
(Save and close the file)

14) Enable the virtual host
sudo a2ensite Item-Catalog
sudo systemctl reload apache2

15) Create the .wsgi File
sudo nano /var/www/Item-Catalog/project.wsgi

(Add the following lines of code to the file:)

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/Item-Catalog/")

from Item-Catalog import app as application
application.secret_key = 'super_secret_key'

16) Restart Apache
sudo /etc/init.d/apache2 restart