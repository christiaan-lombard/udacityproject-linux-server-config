# Udacity Project - Linux Server Configuration

This is my project submission for the Udacity Fullstack Developer Nanodegree, Deployment Module, Linux Server Configuration.


Project is deployed on [Digitalocean](https://www.digitalocean.com/)

 - Hostname: audacious-barnacle
 - IP Address: 209.97.136.125
 - SSH Port: 2200
 - Catalog Project URL: [http://catalog.pubfunc.com/](http://catalog.pubfunc.com/)

## Notes

 - [Deployment Rundown](deployment.md)


## Software Installed

 - apache2
 - libapache2-mod-wsgi
 - libapache2-mod-wsgi-py3
 - postgresql
 - git
 - python-pip


## Resources



## Udacity Project Specifications


This project is linked to the Configuring Linux Web Servers course, which teaches you to secure and set up a Linux server. By the end of this project, you will have one of your web applications running live on a secure web server.

To complete this project, you'll need a Linux server instance. We recommend using Amazon Lightsail for this. If you don't already have an Amazon Web Services account, you'll need to set one up. Once you've done that, here are the steps to complete this project.

### Get your server.

 1. Start a new Ubuntu Linux server instance on Amazon Lightsail. There are full details on setting up your Lightsail instance on the next page.
 2. Follow the instructions provided to SSH into your server.

### Secure your server.

 3. Update all currently installed packages.
 4. Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it.
 5. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

### Give `grader` access.

In order for your project to be reviewed, the grader needs to be able to log in to your server.

 6. Create a new user account named grader.
 7. Give grader the permission to sudo.
 8. Create an SSH key pair for grader using the ssh-keygen tool.

### Prepare to deploy your project.

 9. Configure the local timezone to UTC.
 10. Install and configure Apache to serve a Python mod_wsgi application.
   - If you built your project with Python 3, you will need to install the Python 3 mod_wsgi package on your server: sudo apt-get install libapache2-mod-wsgi-py3.
 11. Install and configure PostgreSQL:
   - Do not allow remote connections
   - Create a new database user named catalog that has limited permissions to your catalog application database.
 12. Install git.

### Deploy the Item Catalog project.

 13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
 14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!