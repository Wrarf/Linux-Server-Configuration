# Linux Server Configuration


## Changes Made (Summary)

### Created a user named grader:
~~~ 
sudo adduser grader
~~~
### Gave to grader the permission to sudo:
~~~
sudo nano /etc/sudoers.d/grader
~~~
And wrote inside the new file
> grader ALL=(ALL:ALL) ALL

### Created a SSH keypair for grader
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
