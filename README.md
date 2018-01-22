Placeholder Draft

Setup and Configure Instance
------
Create an AWS account

Create a Ubuntu Linix-based instance on Lightsail [1]
https://lightsail.aws.amazon.com/ls/docs/getting-started/article/getting-started-with-amazon-lightsail

1. Create new instance i.e. my_instance_name on Lightsail
2. Create and attach a static ip for your Lightsail instance i.e. my_static_ip
3. Note the Public and Private IPs for your instance, and add the following ports:

Static/Public IP: `18.216.39.42`

| Application   | Protocol      | Port Range  |
| ------------- |:-------------:| -----------:|
| SSH           | TCP           | 22          |
| HTTP          | TCP           | 80          |
| CUSTOM        | TCP           | 123         |
| CUSTOM        | TCP           | 2200        |
| CUSTOM        | TCP           | 5000        |
| CUSTOM        | TCP           | 8000        |

4. Find the DNS Address of your Instance. 

There are several sites that allow reverse IP lookup to allow you to do this. [2]

In the case of this instance it is: `http://ec2-18-216-39-42.us-east-2.compute.amazonaws.com`

Update the instance

1. Login to the instance through SSH on your browser.
2. Update the update list:
`sudo apt-get update`
3. Upgrade the distribution:
`sudo apt-get dist-upgrade`
When prompted select to install 'Distribution Maintainers version'.
4. Update the update list again and apply upgrades:
`sudo apt-get update`
`sudo apt-get upgrade`
5. Remove no longer required packages:
`sudo apt autoremove snap-confine`
6. Install Unattended Upgrades with:
`sudo apt-get install unattended-upgrades`
7. Reconfigure the Unattended Upgrades with:
`sudo dpkg-reconfigure -plow unattended-upgrades`
8. Reboot the instance with:
`sudo reboot`
9. Restart the instance.


Create user accounts
------
1. Enter Root:
`sudo su`
2. Set ubuntu password using:
`passwd ubuntu`
You will be asked to set a new UNIX password.
3. Exit Root with exit command:
`exit`
4. Enter ubuntu account as super user:
`sudo su - ubuntu`
5. Create grader user and set UNIX Password:
`sudo adduser grader`
When prompted for Full Name add:
`Udacity Grader` or `Grader`
6. Create grader directory with:
`sudo useradd -m -s /bin/bash grader`
7. Make grader a super user with:
`usermod -aG sudo username`
8. Enter grader account as super user:
`sudo su - grader`
9. Create catalog user and set UNIX Password:
`sudo adduser catalog`
When prompted for Full Name add:
`Database Catalog` or `Catalog`

**User Account Note:** You may be prompted to enter this password, so it is best to note these down.

Set IPs and Ports
------
**Editing Note:** If any of these files are blank, you might not be in the file, if so use:
`cd /etc/` and `sudo nano hosts`
1. Make sure you are logged in as the super user grader:
`sudo su - grader`
2. Enter hosts file:
`sudo nano /etc/hosts`
Underneath the existing IP write:
`STATIC_IP_HERE ubuntu`
Save with ctrl+x and y on prompt (or as directed by nano editor)
3. Add Port 2220 to sshd_config file:
`sudo nano /etc/sshd_config`
4. Underneath the existing Port 22 (or SSH port) write:
`Port 2200`
5. Edit PermitRootLogin prohibit-password to:
`PermitRootLogin no`
6. Edit PasswordAuthentication no to:
`PasswordAuthentication yes`
Save with ctrl+x and y on prompt (or as directed by nano editor)

**Note on Ports:** If you disable Port 22 on Lightsail, you will be unable to login on the Ligthsail web page.
**Note on PermitRootLogin:** Root Login is best kept disabled for security reasons, and only enabled when you need to use it.

Create Instance Snapshot/Backup

**Note on Snapshots:** It is useful to have a snapshot just in case anything goes wrong while using your instance, even if it might take a few minutes to create. You can always delete and add instances as you go.

1. Exit your instance using:
`exit`
2. On the Lightsail web page, click on your instance, and add a snapshot.
3. Wait for it to complete creating your snapshot.

Configure Firewall for your Instance

7. Enter grader account as super user:
`sudo su - grader`
8. Set Firewall with:
```
sudo default ufw deny incoming
sudo default ufw allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/tcp
sudo ufw allow www
sudo ufw allow ssh
```
9. Start Firewall and Check Status with:
```
sudo ufw enable
sudo ufw status
sudo service ssh restart
```
Create SSH Key Pairs
------
**SSH Note:** You can generate new keys, but it is best keeping track of them. Once you have created your SSH Key Pair, you can select and copy to a text editor such as sublime text or notepad++ for later use. Lighsail instances through the browser have a paste window, that you can paste from. 

1. Login as user grader:
`sudo su - grader`
2. Create SSH Key pair with:
`ssh-keygen -t rsa`
When prompted for where to place type: grader
3. While still logged in as grader do the following:
```
sudo mkdir /home/grader/.ssh
sudo chown grader:grader /home/grader/.ssh
sudo chmod 70 /home/grader/ssh
sudo chown grader:grader /home/grader/.ssh/authorized_keys
sudo chmod 644 /home/grader/.ssh/authorized_keys
```
This will apply permissions to the ssh folder and authorized keys to the user grader.

You can now login with:
ssh -i /.ssh/id_rsa grader@YOUR_STATIC_IP_HERE -p 2200

Set Timezone to UTC and Install NTP

1. Login as user grader:
`sudo su - grader`
2. Set Timezone to UTC:
`sudo timedatectl set-timezone UTC`
3. Install NTP:
`sudo apt-get install ntp`
4. Reboot the Instance with:
`sudo reboot`

Installing Packages for Database Application
------
Install APACHE2

1. SSH into the instance.
2. Login in as user grader:
`sudo su - grader`
3. Install Apache2 for the instance with:
`sudo apt-get install apache2`
4. Check that Apache2 is loaded at the Static/Public IP, on your browser:
`http://18.216.39.42`
If so continue to the next step.
5. Install mod-wsgi to allow you to run wsgi files:
`sudo apt-get install libapache2-mod-wsgi`
6. Go to the sites-enabled directory in apache2:
```
cd /etc/apache2/sites-enabled/
ls
```
7. Edit the 000-default.conf file: [Under Edit]
`sudo nano 000-default.conf`
`WSGIScriptAlias / /var/www/html/myapp.wsgi`
8. Restart Apache
`sudo apache2ctl restart`

Installing and Configuring PostgreSQL Users

1. Install PostgreSQL:
`sudo apt-get install postgresql postgresql-contrib`
2. Login as postgres user:
`sudo su - postgres`
3. Create catalog user for PostgreSQL
`createuser --interactive catalog`
When prompted by dialog set:
```
n for super user
y for creating databases and other prompts
db-password for password
```
4. Exit postgres account with:
`exit`

Configure Git 

**Git Note:** It might be a good idea to use the same username and email that you use with gihub.

1. Login to grader user with:
`sudo su - grader`
2. Check that Git is installed with
`sudo apt-get install git`
3. Set global username and email:
`sudo git config -global user.name "your_username`
`sudo git config -global user.email "your_email@emailhost.com"`

Install Additional Packages and Add Application Files
------
**Required Packages Note:** Depending on your instance you might have to install more than on this list.

1. Login to grader user with:
`sudo su - grader`
2. Install the following packages with:
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
sudo pip install flask-seasurf
```
Adding Application Files to Instance

1. Login to grader user with:
`sudo su - grader`
2. Create catalog directory:
`sudo mkdir var/www/catalog/`
3. Move to catalog directory:
 `cp catalog`
4. Clone repository into instance:
`sudo git clone https://github.com/petergns/linix_server_catalog.git var/www/catalog/catalog`
5. Move client json files to previous directory:
`sudo cp var/www/catalog/catalog/client_secrets.json var/www/catalog/client_secrets.json`
`sudo cp var/www/catalog/catalog/fb_client_secrets.json var/www/catalog/fb_client_secrets.json`

Configure Application on Instance
------































