# Deployment Steps

## Local Machine Preperations

 1. Generate a public/private key pair `grader_key` using `ssh-keygen`
 2. Create DNS, A record `catalog.pubfunc.com` with value `209.97.136.125`


## Remote Machine Deployment

 1. Login as `root` or another sudoer to the remote machine

```sh
ssh root@209.97.136.125
```

 2. Configure timezone

```sh
# Configure the local timezone to UTC
echo "Etc/UTC" | sudo tee /etc/timezone
export TZ=Etc/UTC
```

 3. Setup user `grader`

```sh
# create user grader
sudo useradd -c "Udacity Grader" -s "/bin/bash" -m -U grader
# give grader a password
echo -e "supersecret\nsupersecret" | sudo passwd grader
# Give grader the permission to sudo
echo "grader ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/grader
# exit ssh connection as root
exit
```

 4. Login as `grader` with password `supersecret`

```sh
ssh grader@209.97.136.125
```

 5. Authorize your public key

```sh
cd ~
# create .ssh directory
mkdir .ssh
# create authorized_keys file
touch .ssh/authorized_keys
# add public key (previously generated) to authorized_keys
echo "ssh-rsa AA..." > .ssh/authorized_keys
# restrict file permissions
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
# exit ssh connection as grader
exit

# restart connection with private key
ssh grader@209.97.136.125 -i ./grader_key
```

 6. Change ssh port to 2200 and restrict password login:

```sh
sudo vim /etc/ssh/sshd_config
```
```
# updated entries
PasswordAuthentication no
PermitRootLogin no
Port 2200
```
```sh
# Restart ssh
sudo service ssh restart
```

 7. Configure UFW

```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/tcp
sudo ufw enable
```

 8. Update sources, packages and install new packages

```sh
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get upgrade
sudo apt-get install git apache2 python-setuptools python-dev python-pip python-psycopg2 libapache2-mod-wsgi postgresql postgresql-contrib
```

 9. Clone application repository to `~/udacityproject-item-catalog`

```sh
cd ~
git clone https://github.com/christiaan-lombard/udacityproject-item-catalog.git
```

 10. Copy application to `/var/www/catalog`

 ```sh
sudo mkdir /var/www/catalog
sudo mkdir /var/www/catalog/catalog
sudo cp -a ~/udacityproject-item-catalog/src/. /var/www/catalog/catalog
 ```

 11. Configure app python environment

 ```sh

sudo pip install virtualenv

cd /var/www/catalog/catalog

# create temporary environment
sudo virtualenv venv

source venv/bin/activate

sudo pip install Flask
sudo pip install httplib2
sudo pip install requests
sudo pip install --upgrade oauth2client
sudo pip install sqlalchemy
sudo pip install passlib

deactivate

```

12. Create wsgi app launcher

```sh
sudo vim /var/www/catalog/catalog.wsgi
```
```py
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
from catalog import init_db

init_db("postgresql://catalog:secret_db_pass@localhost:5432/catalog", False)
application.config['MAX_CONTENT_LENGTH'] = 4 * 1024 * 1024
application.secret_key = "app_secret"
application.debug = False

```

13. Create apache virtual host

```sh
sudo vim /etc/apache2/sites-available/catalog.conf
```
```apache
<VirtualHost *:80>

    ServerName catalog.pubfunc.com
    ServerAdmin base1.christiaan@gmail.com

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

    ErrorLog ${APACHE_LOG_DIR}/catalog-error.log
    LogLevel info
    CustomLog ${APACHE_LOG_DIR}/catalog-access.log combined

</VirtualHost>
```

```sh
# simlink the new virtual host
cd /etc/apache2/sites-enabled
sudo ln -s ../sites-available/catalog.conf catalog.conf
```


 14. Setup database

 ```sh
# login as postgres
sudo su postgres
psql
```
```sql
CREATE USER catalog WITH PASSWORD 'secret_db_pass';
CREATE DATABASE catalog WITH OWNER catalog;
\q
```
```sh
# logout postgres
exit
```

15. Reload apache

```sh
sudo service apache2 reload
```