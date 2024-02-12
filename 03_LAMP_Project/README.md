# LAMP Stack Project Implementation

---

## In this project, we will be demonstrating the installation of LAMP Server in following steps

# 1. Installing Apache and updating Firewall

### But first we need to update our apps

`sudo apt update`

![Alt text](<Images/Sudo apt update.PNG>)

### Then Install Apache using Ubuntu’s package manager ‘apt’:

`sudo apt install apache2`

![Alt text](<Images/Apache install.PNG>)

### To verify that apache2 is running; run the command

`sudo systemctl status apache2`

![Alt text](<Images/Apache status.PNG>)

## Add rule to AWS EC2 configuration to open inbound connection through port 80.

### To Verify apache2 webpage is accessible from the server; use any of the codes below:

`curl http://localhost:80   or curl http://<public address>:80`

![Alt text](<Images/Apache verified.PNG>)

## Test your server by copying the public address and pasting it on any web browser... It works!!!

![Alt text](<Images/Apache It Work.PNG>)

# 2. INSTALLING MYSQL

### Install Mysql on the ubuntu server

`sudo apt install mysql-server`

![Alt text](<Images/my sql install.PNG>)

### Log into the MySQL console

`sudo mysql`

![Alt text](<Images/mysql login.PNG>)

### Next is to configure a database user and set login password for Mysql using the code. This user's password is defined as 'PassWord.1'

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password.1';`

### After that we exit MYSQL by entering `exit` in the terminal

### Now we start MYSQL Interactive script, this will prompt you to configure the validate password plugin.

`sudo mysql_secure_installation`

![Alt text](<Images/mysql password change.PNG>)

### And also remove anonymous users and access that was automatically installed with MYSQL, by typing 'y' pressing enter when prompted

![Alt text](<Images/mysql password change 2.PNG>)

### Confirm and Check the mysql authentication access by typing the command

`sudo mysql -p`

Then exit the shell with the command: `exit`

![Alt text](<Images/mysql password change success.PNG>)

# 3. INSTALLING PHP

### Here, 3 packages will be installed at once, namely php, libapache2-mod-php, php-mysql.

`sudo apt install php libapache2-mod-php php-mysql`

![Alt text](<Images/php installed.PNG>)

### Confirm the php version

`php -v`

![Alt text](<Images/php version.PNG>)

At this point we have successfully installed all 4 applications that make up the LAMP Stack

- Linux
- Apache Http Server
- MySQL
- PHP

# 4. CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

We will setup a virtual host to test the PHP script, virtual host enables you to setup multiple websites on a single server.

### Create the directory for projectlamp using ‘mkdir’ command

`sudo mkdir /var/www/projectlamp`

### Next, assign ownership of the directory with your current system user:

`sudo chown -R $USER:$USER /var/www/projectlamp`

![Alt text](<Images/projectlamp creation.PNG>)

### Create and open a new configuration file in Apache’s sites-available directory.

`sudo vi /etc/apache2/sites-available/projectlamp.conf`

## Paste below code into the file created above, save and exit.

```

<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

![Alt text](<Images/projectlamp vim.PNG>)

### Enable the new virtual host with the a2ensite command

`sudo a2ensite projectlamp`

### Disable the default site with a2dissite command

`sudo a2dissite 000-default`

### Test your configuration file to ensure it doesn't have syntax error

`$ sudo apache2ctl configtest`

![Alt text](<Images/projectlamp enabled.PNG>)

### After that, reload Apache service for changes to take effect

`sudo systemctl reload apache2`

### Create an index file in the projectlamp folder.

```

sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

```

![Alt text](<Images/Apache test.PNG>)

### Go to your browser and try to open your website URL using IP address:

`http://<Public-IP-Address>:80`

# Awesome!!! It works!

![Alt text](<Images/Apache test 2.PNG>)

# 5. ENABLE PHP ON THE WEBSITE

### Edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

`sudo vim /etc/apache2/mods-enabled/dir.conf`

```

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

```

![Alt text](<Images/php edit.PNG>)

### Restart the apache using the command below.

`sudo systemctl reload apache2`

### Create a new file named index.php inside the projectlamp root folder, enter tyhe code, save and exix.

`vim /var/www/projectlamp/index.php`

```

<?php
phpinfo();

```

![Alt text](<Images/php script.PNG>)

### Refreshing the page; It looks like below

![Alt text](<Images/php success.PNG>)

### It is advisable to remove the file as it contains sensitive information about your server and php site config.

`sudo rm /var/www/projectlamp/index.php`

# ----------------The Job is DONE!!! Thank you!!---------------
