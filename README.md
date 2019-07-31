## Project : Linux Server Configuration 

### I. Start a new Ubuntu Linux server instance on Amazon Lightsail
### Steps :
1. Create a new account with Amazon Lightsail.
2. Create an instance with the specifications mentioned in the course.
3. Once the instance is up, ssh into your instance.
4. You would now be logged in to your instance as ubuntu user.
### Amazon Lightsail instance details
- Instance Name : Ubuntu-4
- Public IP : 13.235.9.217
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

### III. Secure your server instance
### Steps :
1. Update all currently installed packages.
   - Run : $ sudo apt-get update
   - Run : $ sudo apt-get upgrade
2. Configure the uncomplicated firewall.
   - Run : $ sudo ufw status
   It would currently show inactive.
   - Run : $ sudo ufw default deny incoming
   - Run : $ sudo ufw default allow outgoing
   - Run : $ sudo ufw allow 2200/tcp
   - Run : $ sudo ufw allow www
   - Run : $ sudo sudo ufw allow 123
   - Run : $ sudo ufw enable
   - Run : $ sudo sudo ufw status\
   Now, it would be in active status.
3. Change SSH port from 22 to 2200 : Edit the file /etc/ssh/sshd_config by the following command.
   - Run : $ sudo nano /etc/ssh/sshd_config
   Edit the line :
   '# What ports, IPs and protocols we listen for
    Port 22' to 2200
    - Run : $ sudo ufw deny 22
 
 ### IV. Generating key pairs and installing a public key to force key based authentication for secure remote login.
 ### Steps :
 1. Run : $ ssh-keygen in your local machine to generate public/private rsa key pair.
 2. Enter file in which you want to save the key: /home/vagrant/.ssh/linuxCourse.
 3. Enter paasphrase: empty in this case.
 4. Once the keypair is generated, copy the content of linuxCourse.pub 
    - Run : $ cat .ssh/linuxCourse.pub
 5. Now login to your server instance.
    - Run : $ mkdir .ssh
    - Run : $ touch .ssh/authorized_keys to create a file to store the public key
    - Run : $ nano .ssh/authorized_keys and paste the copied content and save the changes
 6. Setup some specefic file permissions.
    - Run : chmod 700 .ssh
    - Run : chmod 644 .ssh/authorized_keys
 7. Now remotely login to your server from your local machine.
    SSH into your instance : $ ssh ubuntu@13.235.9.217 -i -p 22 ~/.ssh/linuxCourse 
   
### III. Create user grader and give sudo access
### Steps :
1. Run : $ sudo adduser grader (password : grader)
2. Run : $ sudo ls /etc/sudoers.d to check the sudoers list.
3. Run : $ sudo nano /etc/sudoers.d/grader
    '# CLOUD_IMG: This file was created/modified by the Cloud Image build process
    grader ALL=(ALL) NOPASSWD:ALL'
    Save the file and exit.
4. Run : $ su - grader to switch to grader user.

### IV. Configure time zone
1. Run : $ sudo timedatectl set-timezone UTC

### V. Install Apache
### Steps :
1. Run : $ sudo apt-get install apache2
2. Run : $ sudo apt-get install libapache2-mod-wsgi-py3
3. Go to http://13.235.9.217/, Ubuntu Default Page will show up.

### VI. Install and configure postgresql
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

### VII. Install git and clone git repository that contains the Catalogapp (ItemCatalog Project)
### Steps :
1. Run : $ sudo apt-get install git
2. Run : $ sudo mkdir /var/www/catalog
3. Run : $ sudo git clone https://github.com/Jayshree-Shinde/fullstack-nanodegree-vm catalog
4. Run : $ sudo chown -R ubuntu:ubuntu catalog/
5. Copy and paste contents of downloaded client_scretes.json to the file with same name under directory        /var/www/catalog/catalog/client_secrets.json
