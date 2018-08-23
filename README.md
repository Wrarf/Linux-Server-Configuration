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
