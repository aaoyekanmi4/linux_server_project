# Linux Server Project README


## Public IP Address: 13.59.6.237

## SSH port: 2200

## Full URL to application: http://ec2-13-59-6-237.us-east-2.compute.amazonaws.com

## About

Configuration of a linux server in order to deploy a web application.
The linux server project is the fifth project for the fullstack nano degree program at udacity. The goal was to deploy the catalog project using amazon's lightsail web service. We connect remotely to the ubuntu server using ssh. Then we add security measures to the server and download the necessary packages to get the site up and running on the internet.

## List of programs installed

- git
- apache2
- mod_wsgi
- finger
- postgresql

Contents of requirements.txt file(pip install)

   - bcrypt==3.1.0
   - Flask==0.11.1
   - httplib2==0.9.2
   - itsdangerous==0.24
   - Jinja2==2.8
   - MarkupSafe==0.23
   - oauth2client==3.0.0
   - psycopg2==2.6.1
   - pyasn1==0.1.9
   - pyasn1-modules==0.0.8
   - pycparser==2.14
   - pytz==2016.4
   - requests==2.11.1
   - rsa==3.4.2
   - six==1.10.0
   - SQLAlchemy==1.1.1
   - Werkzeug==0.11.11



## Steps in configuring the server

1. Updated all packages

   sudo apt-get update
   sudo apt-get upgrade

2. Changed port and restricted root access

   sudo nano /etc/ssh/sshd_config:

       - Changed port to use from 22 to 2200
       - Changed permit root login to no

    Downloaded private key from amazon lightsail to access server from local machine instead of web console

3. Configured firewall

   sudo ufw default deny incoming
   sudo ufw default allow outgoing

   sudo ufw allow ssh
   sudo ufw allow 2200/tcp

   sudo ufw allow www  (port 80)
   sudo ufw allow ntp  (port 123)
   sudo ufw enable

4. Created user grader and set up key based authentication

   Ran command sudo adduser grader to create new user

   Installed finger to keep track of users: sudo apt-get install finger

   Added folder /home/grader/.ssh
   Added file /home/grader/.ssh/authorized_keys

   Generated public and private keys locally by using ssh-keygen
   Stored public key in authorized_keys file on server by copying public  key and pasting into authorized_keys

   Set grader file permissions for ssh and authorized_keys:
   -chmod 700 for ssh
   -chmod 644 for authorized_keys

   Initially ran command sudo usermod -aG sudo grader to give grader sudo  access
   Then added (grader ALL=(ALL) NOPASSWD: ALL) to /etc/sudoers.d/grader to give sudo access without password

6. Set timezone to UTC: timedatectl set-timezone UTC

7. Installed apache

   sudo apt-get install apache2

8. Installed mod_wsgi

   sudo apt-get install libapache2-mod-wsgi

   Configured file in /etc/apache2/sites-enabled/000-default.conf"

       Added the following:

   ```
   ServerName http://13.59.6.237/

   WSGIScriptAlias / /var/www/html/myapp.wsgi

   <Directory C:\var/www/html>

      Order deny,allow

      Allow from all

   </Directory>

   ```

9. Installed postgresql

   sudo apt-get install postgresql postgresql-contrib

   Created both postgres role and user named catalog

   catalog can make databases but is not a superuser

   Created database catalog owned by user catalog


10. Ran sudo apt-get install git


11. Setting up app to run online

   Cloned item-catalog project from  my github repository

   Installed packages in requirements.txt by running pip install -r requirements.txt

   Changed create engine lines in model folder and handlers of project to:

   engine = create_engine('postgresql://catalog:PASSWORD@localhost/catalog')

   Ran python catalog_setup.py to setup a postgresql database

   Ran python instruments.py to populate the database

   Moved the contents of the item-catalog repository to home/ubuntu/var/www/html

   Changed myapp.wsgi to import the item-catalog project:

   ### myapp.wsgi:

   ``` python
   #!/usr/bin/python

   import sys

   import logging

   logging.basicConfig(stream=sys.stderr)

   sys.path.insert(0,"/var/www/html")

   from music_store import app as application

   ```

   Changed file permissions on images folder in project to 777(chmod 777 ~/var/www/html/static/images) so that users could upload photos

   Added public ip address and url to google developer credential section online to allow google oauth2 connections

## Acknowlegements

   Digital Ocean's tutorial on deploying a flask app

   Postgresql documentation

   Flask documentation for deploying app using wsgi

   Udacity FSND program

