# Linux Server Configuration


## Changes Made (Summary)

### Created A User Named Grader
~~~ 
sudo adduser grader
~~~
### Gave To Grader The Permission To Sudo
~~~
sudo nano /etc/sudoers.d/grader
~~~
And wrote inside the new file
> grader ALL=(ALL:ALL) ALL

### Created A SSH Keypair For Grader
Generated with ssh-keypair and added the public key to .ssh/authorized_keys

Changed authorizations
~~~
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
~~~
(.ssh and .ssh/authorized_keys have been created by grader)

Reloaded SSH:
~~~
sudo service ssh restart
~~~

### Changed The SSH Port From 22 To 2200
In /etc/ssh/sshd_config changed
>\# What ports, IPs and protocols we listen for

>Port 22

To

>\# What ports, IPs and protocols we listen for

>Port 2200

### Configured The Uncomplicated Firewall (UFW)
~~~
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200
sudo ufw allow 80
sudo ufw allow 123
sudo ufw allow ssh
sudo ufw allow www
sudo ufw enable
~~~

### Configured The Local Timezone To UTC
~~~
sudo timedatectl set-timezone UTC
~~~

### Configured PostgreSQL
~~~
sudo su - postgres
psql
~~~
Created a new database and user in PostgreSQL shell:
~~~
CREATE DATABASE catalog;
CREATE USER catalog;
ALTER ROLE catalog WITH PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
~~~

### Setup Catalog Project
Cloned the project in /var/www/Catalog

Renamed Application.py (in /var/www/Catalog/Catalog)  to \__\__init____.py
~~~
sudo mv application.py __init__.py
~~~

Removed Vagrantfile
~~~
sudo rm Vagrantfile
~~~

In \__\__init____.py, populate_db.py and database_setup.py changed from 
~~~
engine = create_engine('sqlite:///catalog.db')
~~~
To
~~~
engine = create_engine('postgresql://catalog:password@localhost/catalog')
~~~

Initialized the database
~~~
sudo python populate_db.py
~~~

### Configured The Virtual Host
Created /etc/apache2/sites-available/FlaskApp.conf and wrote inside it
~~~
<VirtualHost *:80>
        ServerName 18.197.157.77
        ServerAdmin capitantaka@gmail.com
        WSGIScriptAlias / /var/www/Catalog/catalog.wsgi
        <Directory /var/www/Catalog/Catalog/>
                Order allow,deny
                Allow from all
        </Directory>
        Alias /static /var/www/Catalog/Catalog/static
        <Directory /var/www/Catalog/Catalog/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~

Enabled the virtual host
~~~
sudo a2ensite Catalog
~~~

### Created The .wsgi File
Created /var/www/Catalog/catalog.wsgi and wrote inside it
~~~
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/Catalog/")
~~~


## Packages Installed

- apache2
- libapache2-mod-wsgi
- postgresql
- python-pip

(the following have been installed using pip)

- sqlalchemy
- flask
- oauth2client
- requests
- psycopg2
