# Linux_Server_Configuration_UDProject5
Linux Server Configuration Udacity project5

## Descitption
Install a Linux server and prepare it to host web applications. Secure the server from a number of attack vectors, install and configure a database server, and deploy an existing web applications onto it.

## Dependency
1. AWS lightsail
2. python 2.7
3. sqlalchemy
4. flask
5. wsgi

## The IP address and SSH port so your server can be accessed by the reviewer
1. IP address: 18.222.21.107
2. SSH port: 80

## The complete URL to your hosted web application
http://18.222.21.107 Not sure.

## Installation
### 1. Register on AWS lightsail service.
Get started on Lightsail
We're recommending Amazon Lightsail for this project. If you prefer, you can use any other service that gives you a publicly accessible Ubuntu Linux server. But Lightsail works pretty well and it's what we've tested.

There are a few things you need to do when you create your server instance.

1. Log in!
First, log in to Lightsail. If you don't already have an Amazon Web Services account, you'll be prompted to create one.
Amazon Web Services login page
Amazon Web Services login page.
2. Create an instance.
Once you're logged in, Lightsail will give you a friendly message with a robot on it, prompting you to create an instance. A Lightsail instance is a Linux server running on a virtual machine inside an Amazon datacenter.
Lightsail screenshot: No instances
When you have no instances, Lightsail gives you a picture of an orange robot and suggests that you create an instance.
3. Choose an instance image: Ubuntu
Lightsail supports a lot of different instance types. An instance image is a particular software setup, including an operating system and optionally built-in applications.

For this project, you'll want a plain Ubuntu Linux image. There are two settings to make here. First, choose "OS Only" (rather than "Apps + OS"). Second, choose Ubuntu as the operating system.
Lightsail setup: Pick your instance image (Ubuntu)
When you create an instance, Lightsail asks what kind you want.
For this project, choose an "OS Only" instance with Ubuntu.
4. Choose your instance plan.
The instance plan controls how powerful of a server you get. It also controls how much money they want to charge you. For this project, the lowest tier of instance is just fine. And as long as you complete the project within a month and shut your instance down, the price will be zero.
Lightsail setup: Pricing options. (Choose $5/month with first month free.)
Lightsail's options for instance pricing.
For this project, pick the lowest one to get free-tier access.
Be aware: If you enable additional features in Lightsail, you may be charged extra for them.
5. Give your instance a hostname.
Every instance needs a unique hostname. You can use any name you like, as long as it doesn't have spaces or unusual characters in it. Your instance's name will be visible to you and to the project reviewer.
Lightsail setup: Setting a name for your instance.
I've named my instance silly-name-here.
6. Wait for it to start up.
It may take a few minutes for your instance to start up.
Lightsail: Instance starting.
While your instance is starting up, Lightsail shows you a grayed-out display.
Lightsail: Instance running.
Once your instance is running, the display gets brighter.
7. It's running; let's use it!
Once your instance has started up, you can log into it with SSH from your browser.

The public IP address of the instance is displayed along with its name. In the above picture it's 54.84.49.254.

Note: When you set up OAuth for your application, you will need a DNS name that refers to your instance's IP address. You can use the xip.io service to get one; this is a public service offered for free by Basecamp. For instance, the DNS name 54.84.49.254.xip.io refers to the server above.
Lightsail: Main page for an instance.
The main page for my silly-name-here instance.
The big orange "Connect using SSH" button is the next step.
Explore the other tabs of this user interface to find the Lightsail firewall and other settings. You'll need to configure the Lightsail firewall as one step of the project.

When you SSH in, you'll be logged as the ubuntu user. When you want to execute commands as root, you'll need to use the sudo command to do it.
Lightsail: SSH window.
An SSH window logged into the server instance.
From here, it's just like any other Linux server.
8. Project time.
Now that you have a working instance, you can get right into the project!

### 2. Secure the server.
1. Update all installed packages.
Using SSH connecting on light sail UI to ssh into the server.
To update the packages:
sudo apt update
sudo apt upgrade

2. Change SSH port from 22 to 2200. Configure the lightsail firewall.
You need to configure the sshd_config under etc/ssh/
sudo nano /etc/ssh/sshd_config
to edit the port number under the file.

3. Configure fire wll in server and lightsail services.
Use following commands to configure the server.
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw limit 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable

And in networking part in lightsail, edit the Firewall part.
click add another to add custom application, TCP protocol and port range to the needed port: 123, 2200, 80.

Mention if you have made it right, the default ssh connection on the lightsail UI should not lead you to the ternimal.
Because the SSH default port 22 have been blocked.

### 3. Add a grader access.
1. create a grader user.
sudo adduser grader
Have to set password here. I set grader as the password. But will use auth so it doesnt matter.

2. give grader permission to sudo.
sudo usermod -aG sudo grader

3. Create an SSH key pair for grader using the ssh-keygen tool
first in local machine. Use ssh-keygen to generate key pair.
Locate to /.ssh/ directory under the user name directory.
For me:
cd /User/wang8zai/.ssh/
After this use:
ssh-keygen -t rsa
generate the key pair. should get a pair of files.
Like key and key.pub
key is used as private key on local machine and key.pub is used to store on the server.

You need to add the grader public key which means key.pub here to the server.

sudo mkdir /home/grader/.ssh
sudo chown grader:grader /home/grader/.ssh # changing ownership of .ssh to grader
sudo chmod 700 /home/grader/.ssh           # change folder permission
sudo cp /home/ubuntu/.ssh/authorized_keys /home/grader/.ssh/
sudo chmod 644 /home/grader/.ssh/authorized_keys

Set the permission to the folder and file.
And the public key is stored in the authorized_keys.

To log in as grader.
sudo ssh -i ~/.ssh/key grader@18.222.21.107 -p 2200
~/.ssh/key is the private key and grader is the user name , and then ip address. 2200 the port number.

### 4. Prepare to deploy your project.
9. Configure the local timezone to UTC.
Simple search.
sudo timedatectl set-timezone UTC

10. Install and configure Apache to serve a Python mod_wsgi application.
Install dependencies.
sudo apt-get install apache2 libapache2-mod-wsgi

11. Install and configure PostgreSQL:
1. install postgres:
sudo apt-get install postgresql
2. configure:

Add a user called catalog with password catalog
build a db along with this catalog user.
sudo -u postgres
psql
create user catalog with password 'catalog';
create database catalog owner catalog;
\q
exit

12. Install git.
sudo apt-get install git

### 5. Deploy the Item Catalog project.
13. Clone and setup your Item Catalog project from the Github repository you created earlier in this Nanodegree program.
clone to server:
sudo git clone https://github.com/wang8zai/fullstack-nanodegree-vm /var/www

14. Set it up in your server so that it functions correctly when visiting your server’s IP address in a browser. Make sure that your .git directory is not publicly accessible via a browser!
To specify: my main python file is here:
/var/www/fullstack-nanodegree-vm/vagrant/catalog/webserver.py

Need to add a .wsgi file under ../vagrant/project.wsgi
And this project.wsji is directed by the 000-default.conf under:
/etc/apache2/sites-enabled/000-default.conf

Need to update 000-default.conf.
Update to something like this:
<VirtualHost *:80>
	# The ServerName directive sets the request scheme, hostname and port that
	# the server uses to identify itself. This is used when creating
	# redirection URLs. In the context of virtual hosts, the ServerName
	# specifies what hostname must appear in the request's Host: header to
	# match this virtual host. For the default virtual host (this file) this
	# value is not decisive as it is used as a last resort host regardless.
	# However, you must set it for any further virtual host explicitly.
	#ServerName www.example.com

	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
	# error, crit, alert, emerg.
	# It is also possible to configure the loglevel for particular
	# modules, e.g.
	#LogLevel info ssl:warn

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined

	# For most configuration files from conf-available/, which are
	# enabled or disabled at a global level, it is possible to
	# include a line for only one particular virtual host. For example the
	# following line enables the CGI configuration for this host only
	# after it has been globally disabled with "a2disconf".
	#Include conf-available/serve-cgi-bin.conf
	WSGIScriptAlias / /var/www/fullstack-nanodegree-vm/vagrant/catalog.wsgi
	<Directory /var/www/fullstack-nanodegree-vm/vagrant/catalog/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/fullstack-nanodegree-vm/vagrant/catalog/static
	<Directory /var/www/fullstack-nanodegree-vm/vagrant/catalog/static/>
		Order allow,deny
		Allow from all
	</Directory>
</VirtualHost>

The last directory part is the most updated place.

Should test this directory works or not by visit the public IP address with a function named application specified in the catalog.wsgi file.

Finally, we can update the wsgi file to:

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
#specify the path of the files.
sys.path.insert(0,"/var/www/fullstack-nanodegree-vm/vagrant/catalog")

from webserver import app as application
application.secret_key = 'secretkey'

15. Other modifications:
1. To change to postgres:
engine = create_engine('postgresql://catalog:catalog@localhost/catalog')
the first catalog is the user name. Second password. third db name.
Actually to change from sqlite to postgresql the very much work you need to do is the above change.
Also mention postgresql does not support same thread. So get rid of that.

16. Some other settings:
Need to make all dependency work.
sudo pip install Flask
sudo pip install httplib2
sudo pip install requests
sudo pip install oauth2client
sudo pip install sqlalchemy
And for me I also added a -H there. 
Ex. sudo -H pip install Flask

## Some other points.
1. I didnt memtion I set the virtual environment because in the end I dont know it is needed or not?
2. The google log in does not work, didnt figure it out yet.
3. modifed a lot in the origin project3 because some change from sqlite to postgres. And some ohter errors.

