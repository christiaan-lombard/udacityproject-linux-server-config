# Deployment Rundown

With `root` or another sudoer:

Setup user `grader`

```sh
# Give grader access
sudo useradd -c "Udacity Grader" -s "/bin/bash" -m -U grader
# give grader a password
echo -e "supersecret\nsupersecret" | sudo passwd grader
# Give grader the permission to sudo
echo "grader ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/grader
```

Now login as `grader`:

Authorize a public key

```sh
# create .ssh directory
mkdir .ssh
# create authorized_keys file
touch .ssh/authorized_keys
# add public key to authorized_keys
echo "ssh-rsa AA..." > .ssh/authorized_keys
# restrict file permissions
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

Update packages and install required packages

```sh
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install apache2 libapache2-mod-wsgi libapache2-mod-wsgi-py3 postgresql git python-pip
```

Configure timezone

```sh
# Configure the local timezone to UTC
echo "Etc/UTC" | sudo tee -a /etc/timezone
export TZ=Etc/UTC
```

Change ssh port to 2200 and restrict password login:

```sh
sudo vim /etc/ssh/sshd_config
# set PasswordAuthentication no
# set Port 2200

# Restart ssh
sudo service ssh restart
```

Configure UFW

```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/tcp
```

Setup DB, see [Stackoverflow - How to configure postgresql for the first time?](https://stackoverflow.com/questions/1471571/how-to-configure-postgresql-for-the-first-time)

Using `psql template1`, run:

```sql
CREATE DATABASE catalog;
\c catalog
```