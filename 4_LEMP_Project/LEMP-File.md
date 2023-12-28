# AWS LEMP STACK IMPLEMENTATION

# This project shows how to implement LEMP stack (Linux-Nginx-Mysql-Php) on AWS in a sequential steps.

# STEP 1 - INSTALLING THE NGINX WEBSERVER

### First we start by updating our list of packages in package manager

`sudo apt update`

![Alt text](<Images/apt update.PNG>)

### Next we install the Nginx package

`sudo apt install nginx`

![Alt text](<Images/nginx install.PNG>)

### Verify if Nginx is Up and running

`sudo systemctl status nginx`

![Alt text](<Images/nginx status.PNG>)

## Add rule to AWS EC2 configuration to open inbound connection through port 80. This is done already on AWS...

## Also verify the web server is reachable from the localhost by running any of the codes below:

```

 curl http://localhost:80

or

 curl http://127.0.0.1:80

```

![Alt text](<Images/webserver verified.PNG>)

### Test and View the Nginx server on any web browser using the public IP address

`http://<Public-IP-Address>:80`

![Alt text](<Images/nginx webpage.PNG>)

# STEP 2 - INSTALLING MYSQL

---

### Install MYSQL package using the code below

`sudo apt install mysql-server -y`

![Alt text](<Images/mysql installed.PNG>)

### Log into Mysql console.

`sudo mysql`

### Run a security script to set root password.

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password.1'`

### After that, exit mysql by typing `exit`

### The above demostration codes can be seen below:

![Alt text](<Images/mysql login.PNG>)

### Run the security installation, this will require you to change the password, and also remove some insecure default settings that comes with mysql, using the code below:

`sudo mysql_secure_installation`

![Alt text](<Images/mysql login 2.PNG>)

### Run the code below to test mysql log-in

`sudo mysql -p`

### After succcessfully confirmimg the login, you can exit MYSQL by typing `exit`

![Alt text](<Images/mysql password success.PNG>)

# STEP 3 - INSTALLING PHP

---

### Install PHP and its dependencies.

`sudo apt install php-fpm php-mysql -y`

![Alt text](<Images/php installed.PNG>)

# STEP 4 - CONFIGURING NGINX TO USE PHP

### First create a folder called projectLEMP for our webserver. Create the root web directory for your domain in /var/www/ folder as follows:

`sudo mkdir /var/www/projectLEMP`

### Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

### Open a new configuration file in Nginx’s sites-available directory

`sudo nano /etc/nginx/sites-available/projectLEMP`

![Alt text](<Images/php directory.PNG>)

![Alt text](<Images/php code.PNG>)

### Save and close the file.

### Activate the configuration by linking the config file from Nginx’s sites-enabled directory.

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

### Test for syntax error

`$ sudo nginx -t`

![Alt text](<Images/php config success.PNG>)

### Disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

### Reload NGINX to apply these changes.

`sudo systemctl reload nginx`

### Create an index.html file in the location /var/www/projectLEMP to test and see if your new server block works as expected by running the code below:

```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

![Alt text](<Images/LEMP file.PNG>)

### Now let's try to open the website URL from any browser using IP address or DNS name:

`http://<Public-IP-Address>:80` or `http://<Public-DNS-Name>:80`

![Alt text](<Images/LEMP success.PNG>)

# STEP 5 - TESTING PHP WITH NGINX

---

### This can be done this by creating a test PHP file in the document root.

### Open a new file called info.php:

`sudo nano /var/www/projectLEMP/info.php`

![Alt text](<Images/php test.PNG>)

### Test run it on web browser:

`http://server_domain_or_IP/info.php`

![Alt text](<Images/php success.PNG>)

### You can always remove the file above as it contain very sensitive information:

`sudo rm /var/www/your_domain/info.php`

# STEP 6 - RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

---

### Create a database named lemp_database and a user named lemp_user.

### First, connect to the MySQL console using the root account and password:

`sudo mysql -p`

### Create a new database and user.

`mysql> CREATE DATABASE `example_database `;`

### Create a new user and grant it full privileges on the database.

`mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'Password.1';`

### Give this user permission over the lemp_database database:

`mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

### This will give the lemp_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

### Exit the MySQL shell by typing: `exit`

### Above codes can be illustrated in the image below.

![Alt text](<Images/mysql user.PNG>)

### Log in to `example_database` with the new `example_user` account created.

`mysql -u example_user -p example_database`

### Confirm we have access to `example_database`.

`mysql> SHOW DATABASES;`

### Next, create a test table named todo_list with the following statement:

```
 CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
```

### Insert a few rows of content in the example table by repeating the next command a few times, using different VALUES:

```
INSERT INTO example_database.todo_list (content) VALUES ("My first database item");
INSERT INTO example_database.todo_list (content) VALUES ("My second important item");
```

![Alt text](<Images/mysql list.PNG>)

### To see the contents of the table, run the command:

`mysql> select * from example_database.todo_list;`

### After that, exit MYSQL by typing `exit`

![Alt text](<Images/mysql list success.PNG>)

### Create a PHP script that will connect to MySQL and query the content by creating a new PHP file in the custom web root directory.

`nano /var/www/projectLEMP/todo_list.php`

![Alt text](<Images/php to_do list.PNG>)

### Save and close the file.

### Now access the todo_list.php page from the web browser

`http://<Public_domain_or_IP>/todo_list.php`

![Alt text](<Images/php to do success.PNG>)

# ....... All Done! .......
