# Linux Server Setup
####   Instructions for setting up a Linux server using Amazon Lightsail.

## Project Info
#### Public IP address 34.207.201.164 username: grader password: udacity
#### requires ssh key

## Lightsail Setup

1. Login to AWS console.
1. Create new AWS account
1. Go to https://lightsail.aws.amazon.com/ and click: create instance.
1. Select OS only and select Ubuntu
1. Name instance
1. Go to networking tab and create allowances for all required ports (if it's not allowed here it won't work even if ufw allows it.)
1. Create ssh key locally and be sure to change permissions to allow only you to edit with sudo chmod 700
1. Upload your ssh key to Lightsail.
1. Create user grader with sudo adduser grader (my password is set to udacity)
1. Create file with sudo nano /etc/sudoers.d/grader with a line reading: grader ALL=(ALL:ALL) ALL  Then save and close the file.
1. Update, Upgrade, and install finger
1. Return to previously created ssh key and copy it.
1. Move to /home/grader
1. Create .ssh directory: mkdir .ssh
1. Create file to store public keys: touch .ssh/authorized_keys
1. Edit authorized_keys: nano .ssh/authorized_keys and copy the previous ssh key into the file. Save and close.
1. Change permissions: $ sudo chmod 700 /home/grader/.ssh and $ sudo chmod 644 /home/grader/.ssh/authorized_keys
1. Change owner: $ sudo chown -R grader:grader /home/grader/.ssh
1. Restart ssh: $ sudo service ssh restart
1. Logout and log back in
### Configure Firewall
  1. $ sudo ufw status
  1. $ sudo ufw default deny incoming
  1. $ sudo ufw allow outgoing
  1. $ sudo ufw allow 2200/tcp
  1. $ sudo ufw allow www
  1. $ sudo ufw allow ntp
  1. $ sudo ufw enable
### Install for server
1. Set timezone to UTC: $ sudo dpkg-reconfigure tzdata
1. Install apache2: $ sudo apt-get install apache2
1. Install wsgi: $ sudo apt-get install libapache2-mod-wsgi
1. Restart apache2: $ sudo service apache2 restart
### Postgresql
1. Install postgresql: $ sudo apt-get install postgresql
1. Change to postgres user: $ sudo su - postgres
1. Access postgresql: $ psql
1. Create db: CREATE DATABASE catalog;
1. CREATE USER catalog;
1. ALTER ROLE catalog WITH PASSWORD 'catalog';
1. GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
### Clone repository
1. Create directory for project: $ cd /var/www
1. $ mkdir catalog
1. $ cd catalog
1. cloned git repository into /var/www/catalog/catalog/
### Setting up Project
1. change name of app.py to __init__.py
1. installed all requirements onto server
1. changed references to files to specific location in server
1. changed references to db to refer to catalog db created on server previously

### Server Configuration
1. created file: $ sudo nano /etc/apache2/sites-available/catalog.conf
1. Inserted:
        <VirtualHost * :80>
                ServerName 34.207.201.164
                ServerAdmin gertner.code@gmail.com
                WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                <Directory /var/www/catalog/catalog/>
                        Order allow,deny
                        Allow from all
                </Directory>
                Alias /static /var/www/catalog/catalog/static
                <Directory /var/www/catalog/catalog/static/>
                        Order allow,deny
                        Allow from all
                </Directory>
                ErrorLog ${APACHE_LOG_DIR}/error.log
                LogLevel warn
                CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>

1. create file $ sudo nano /var/www/catalog/catalog.wsgi
1. Inserted:
        #!/usr/bin/python
        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/var/www/catalog/")

        from catalog import app as application
        application.secret_key = 'super_secret_key'

1. sudo service apache2 restart
