## Project : Linux Server Configuration 

### I. Start a new Ubuntu Linux server instance on Amazon Lightsail
### Steps :
1. Create a new account with Amazon Lightsail.
2. Create an instance with the specifications mentioned in the course.
3. Once the instance is up, ssh into your instance.
4. You would now be logged in to your instance as ubuntu user.
### Amazon Lightsail instance details
- Instance Name : Ubuntu-5
- Public IP : 13.235.94.112
- Port : 22

### II. Set up your local machine environment
### Steps :
1. You might be already using VirtualBox and Vagrant for your previous projects. Else, download and install them.
2. Create a new folder on your computer where you’ll store your work.
3. cd into that folder within your terminal.
4. Tell Vagrant what kind of Linux virtual machine you would like to run.
   - Run : $ vagrant init ubuntu/trusty64
5. Start the virtual machine
   - Run : $ Vagrant up
   - Run : $ Vagrant provision
   - Run : $ Vagrant ssh
6. You would now be logged in as vagrant user.

### III. Secure your server instance and automatically install updates
### Steps :
1. Update all currently installed packages.
   - Run : $ sudo apt-get update
   - Run : $ sudo apt-get upgrade
   - Run : $ sudo apt-get install unattended-upgrades
   - Run : $ sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
     and uncomment the line ${distro_id}:${distro_codename}-updates and save it
   - Run : $ sudo /etc/apt/apt.conf.d/20auto-upgrades
     and add the below lines so that the upgrades are downloaded and installed every day.
     ```
     	APT::Periodic::Update-Package-Lists "1";APT::Periodic::Download-Upgradeable-Packages "1";APT::Periodic::AutocleanInterval "7"; APT::Periodic::Unattended-Upgrade "1";
	
    - Run : $ sudo dpkg-reconfigure --priority=low unattended-upgrades to enable automatic upgrades
    - Run : $ sudo service apache2 restart
2. Configure the uncomplicated firewall.
   - Run : $ sudo ufw status
   It would currently show inactive.
   - Run : $ sudo ufw default deny incoming
   - Run : $ sudo ufw default allow outgoing
   - Run : $ sudo ufw allow 2200/tcp
   - Run : $ sudo ufw allow www
   - Run : $ sudo sudo ufw allow 123
   - Run : $ sudo ufw enable
   - Run : $ sudo sudo ufw status
   Now, it would be in active status.
3. Change SSH port from 22 to 2200 : Edit the file /etc/ssh/sshd_config by the following command.
   - Run : $ sudo nano /etc/ssh/sshd_config
   Edit the line :
   '# What ports, IPs and protocols we listen for
    Port 22' to 2200
    - Run : $ sudo ufw deny 22
 
### IV. Create user grader and give sudo access
### Steps :
1. Run : $ sudo adduser grader (password : grader)
2. Run : $ sudo ls /etc/sudoers.d to check the sudoers list.
3. Run : $ sudo nano /etc/sudoers.d/grader
    '# CLOUD_IMG: This file was created/modified by the Cloud Image build process
    grader ALL=(ALL) NOPASSWD:ALL'
    Save the file and exit.
 4. Run : $ su - grader to switch to grader user.
 
 ### V. Generating key pairs and installing a public key to force key based authentication for secure remote login.
 ### Steps :
 1. Run : $ ssh-keygen in your local machine to generate public/private rsa key pair.
 2. Enter file in which you want to save the key: /home/vagrant/.ssh/graderCourse.
 3. Enter paasphrase: empty in this case.You will see the following message
    Your identification has been saved in /home/vagrant/.ssh/graderCourse.
    Your public key has been saved in /home/vagrant/.ssh/graderCourse.pub.
 4. Once the keypair is generated, copy the content of linuxCourse.pub 
    - Run : $ cat .ssh/graderCourse.pub
 5. Now login to your server instance as user grader.
    - Run : $ mkdir .ssh
    - Run : $ touch .ssh/authorized_keys to create a file to store the public key
    - Run : $ nano .ssh/authorized_keys and paste the copied content and save the changes
 6. Setup some specefic file permissions.
    - Run : sudo chmod 700 .ssh
    - Run : sudo chmod 644 .ssh/authorized_keys
    - Run : sudo chown -R grader .ssh
    - Run : sudo chgrp -R grader .ssh
 7. Now remotely login to your server from your local machine.
    SSH into your instance : $ ssh -i ~/.ssh/graderCourse grader@13.235.94.112 
 8. Configure SSH (restrict root login and set password)
    - Run : sudo nano /etc/ssh/sshd_config
    Set PasswordAuthentication no
    Set PermitRootLogin no
    
### VI. Configure time zone
1. Run : $ sudo timedatectl set-timezone UTC

### VII. Install Apache
### Steps :
1. Run : $ sudo apt-get install apache2
2. Run : $ sudo apt-get install libapache2-mod-wsgi-py3
3. Go to http://13.235.94.112/, Ubuntu Default Page will show up.

### VIII. Install and configure postgresql
### Steps :
1. Run : $ sudo apt-get install postgresql
2. Run : $ sudo su - postgre
3. Run : $ psql
4. Run : postgres=# CREATE ROLE catalog WITH LOGIN PASSWORD 'catalog'; to create catalog user.
5. Run : postgres=# ALTER ROLE catalog CREATEDB; to give user ability to create database.
6. Run : \q to exit to switch back to grader 
7. Run : sudo adduser catalog (password : catalog)
8. Run :  $ sudo nano /etc/sudoers.d/grader
    '# CLOUD_IMG: This file was created/modified by the Cloud Image build process
    catalog  ALL=(ALL:ALL) ALL'
    Save the file and exit.
9. Run : $ sudo su - catalog
10.Run : $ createdb catalog and exit

### IX. Install git and clone git repository that contains the Catalogapp (ItemCatalog Project)
### Steps :
1. Run : $ sudo apt-get install git
2. Run : $ sudo mkdir /var/www/catalog
3. Run : $ sudo git clone https://github.com/Jayshree-Shinde/Udacity-fullstack-webdeveloper catalog
4. Run : $ sudo chown -R ubuntu:ubuntu catalog/
5. Copy and paste contents of downloaded client_scretes.json to the file with same name under directory        /var/www/catalog/catalog/client_secrets.json
   Run : $ sudo touch client_secrets.json
   Run : $ sudo nano client_secrets.json
 6. Change file project.py to init.py: 
    - Run : $ sudo mv Catalog.py __init__.py

 
 ### VIII. Install other required packages
 ### Steps :
 1. Run : $ sudo apt-get install python-psycopg2 python-flask
 2. Run : $ sudo apt-get install python-sqlalchemy python-pip
 3. Run : $ sudo pip install --upgrade pip
 4. Run : $ sudo pip install oauth2client
 5. Run : $ sudo pip install requests
 
 ### IX. Setup a virtual host and run the final application
 ### Steps :
 1. Run : $ sudo touch /etc/apache2/sites-available/catalog.conf
 2. Run : $ sudo nano /etc/apache2/sites-available/catalog.conf and add the following
   ```
   <VirtualHost *:80>
		ServerName 13.235.9.217
		ServerAdmin admin@13.235.9.217
		WSGIScriptAlias / /var/www/catalog.wsgi
		<Directory /var/www/catalog/>
			Order allow,deny
			Allow from all
			Options -Indexes
		</Directory>
		Alias /static /var/www/catalog/static
		<Directory /var/www/catalog/static/>
			Order allow,deny
			Allow from all
			Options -Indexes
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```
 3. Run : $ sudo a2ensite catalog
 4. Run : $ udo touch /var/www/catalog/catalog.wsgi and add the following
  ```
  #!/usr/bin/python
   import sys
   import logging
   logging.basicConfig(stream=sys.stderr)
   sys.path.insert(0,"/var/www/catalog/")
   sys.path.insert(1,"/var/www/catalog/catalog")

   from catalog import app as application
   application.secret_key = 'super_secret_key'
   ```
  5. Run : $ sudo nano Project2_databaseSetup.py and replace the below line
    engine = create_engine('sqlite:///catalogapp.db')
    with
    engine = create_engine ('postgresql://catalog:catalog@localhost/catalog')
  6. Run : sudo nano Catalog.py
    Replace the below line
    app.run(host='0.0.0.0', port=5000)
    with
    app.run()
  7. Run : $ sudo python Catalog.py
  
