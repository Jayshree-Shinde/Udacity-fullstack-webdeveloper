## Project : Linux Server Configuration 

### I. Start a new Ubuntu Linux server instance on Amazon Lightsail
### Steps :
1. Create a new account with Amazon Lightsail.
2. Create an instance with the specifications mentioned in the course.
3. Once the instance is up, ssh into your instance.
4. You would now be logged in to your instance as ubuntu user.
### Amazon Lightsail instance details
Instance Name : Ubuntu-4
Public IP : 13.235.9.217
Port : 22

### II. Set up your local machine environment
### Steps :
1. You might be already using VirtualBox and Vagrant for your previous projects. Else, download and install them.
2. Create a new folder on your computer where you’ll store your work.
3. cd into that folder within your terminal.
4. Tell Vagrant what kind of Linux virtual machine you would like to run.
   Run : $ vagrant init ubuntu/trusty64
5. Start the virtual machine
   Run : $ Vagrant up
   Run : $ Vagrant provision
   Run : $ Vagrant ssh
6. You would now be logged in as vagrant user.

### III. Secure your server instance
### Steps :
1. Update all currently installed packages.
   Run : $ sudo apt-get update
   Run : $ sudo apt-get upgrade
2. Configure the uncomplicated firewall.
   Run : $ sudo ufw status
   It would currently show inactive.
   Run : $ sudo ufw default deny incoming
   Run : $ sudo ufw default allow outgoing
   Run : $ sudo ufw allow 2200/tcp
   Run : $ sudo ufw allow www
   Run : $ sudo sudo ufw allow 123
   Run : $ sudo ufw enable
   Run : $ sudo sudo ufw status\
   Now, it would be in active status.
3. Change SSH port from 22 to 2200 : Edit the file /etc/ssh/sshd_config by the following command.
   Run : $ sudo nano /etc/ssh/sshd_config
   Edit the line :
   '# What ports, IPs and protocols we listen for
    Port 22' to 2200
    Run : $ sudo ufw deny 22
 
 ### IV. Generating key pairs and installing a public key to force key based authentication for secure remote login.
 ### Steps :
 1. Run : $ ssh-keygen in your local machine to generate public/private rsa key pair.
 2. Enter file in which you want to save the key: /home/vagrant/.ssh/linuxCourse.
 3. Enter paasphrase: empty in this case.
 4. Once the keypair is generated, copy the content of linuxCourse.pub 
    Run : $ cat .ssh/linuxCourse.pub
 5. Now login to your server instance.
    Run : $ mkdir .ssh
    Run : $ touch .ssh/authorized_keys to create a file to store the public key
    Run : $ nano .ssh/authorized_keys and paste the copied content and save the changes
 6. Setup some specefic file permissions.
    Run : chmod 700 .ssh
    Run : chmod 644 .ssh/authorized_keys
 7. Now remotely login to your server from your local machine.
    SSH into your instance : $ ssh ubuntu@13.235.9.217 -i -p 22 ~/.ssh/linuxCourse 
   
   
### III. Log into your server instance
### Steps :
1. 

