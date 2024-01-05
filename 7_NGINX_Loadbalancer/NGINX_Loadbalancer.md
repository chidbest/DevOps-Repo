# IMPLEMENTATION OF LOAD BALANCING WITH NGINX

## Setting Up a Basic Load Balancer

### Step 1: Provision two EC2 instances running ubuntu 22.04 operating system and install apache webserver in them. We will open port 8000 to allow traffic from anywhere.

![Alt text](<Images/1.Apache servers.PNG>)

_Two EC2 Instances created running ubuntu 22.04 as shown above_

![Alt text](<Images/3.Apache 1 details.PNG>)

_Screenshot of Apache server 1 above showing IP details_

![Alt text](<Images/3.Apache 2 details.PNG>)

_Screenshot of Apache server 2 above showing IP details_

### Step 2: Open port 8000. We will be running our two webservers on port 8000 while the load balancer runs on port 80. We need to open port 8000 to allow traffic from anywhere by adding a rule to the security group of each of our webservers.

![Alt text](<Images/2.server security.PNG>)

### Step 3: SSH into the two servers and install Apache on them.

`sudo apt update -y &&  sudo apt install apache2 -y`

![Alt text](<Images/5.server 1 Apache installed.PNG>) ![Alt text](<Images/6.server 2 Apache installed.PNG>)

- Verify apache is running on both instances using `sudo systemctl status apache2`

![Alt text](<Images/7.server 1 Apache active.PNG>) ![Alt text](<Images/7.server 2 Apache active.PNG>)

### Step 4: Configure Apache to serve a page showing its public IP address.

- Configure Apache to serve content on port 8000 by adding the port to the already existing port on `sudo nano /etc/apache2/ports.conf`

![Alt text](<Images/8.server 1 config.PNG>) ![Alt text](<Images/8.server 2 config.PNG>)

- Open the file /etc/apache2/sites-available/000-default.conf and change port 80 on the virtualhost to 8000. Save and Close the file

`sudo nano /etc/apache2/sites-available/000-default.conf`

![Alt text](<Images/9.server 1 port.PNG>) ![Alt text](<Images/9.server 2 port.PNG>)

- Restart apache to load the new configuration using the command `sudo systemctl restart apache2`

![Alt text](<Images/10.server 1 codes.PNG>) ![Alt text](<Images/10.server 2 codes.PNG>)

- Create a new html file `sudo nano index.html`.

- Copy the code below and paste in the new html files. Replace YOUR_PUBLIC_IP with the respective public IP's of the two apache servers.

```
<!DOCTYPE html>
<html>
<head>
    <title>My EC2 Instance</title>
</head>
<body>
    <h1>Welcome to my EC2 instance</h1>
    <p>Public IP: YOUR_PUBLIC_IP</p>
</body>
</html>
```

_As shown below for both servers:_

![Alt text](<Images/11.server 1 html.PNG>) ![Alt text](<Images/11.server 2 html.PNG>)

- Change ownership of the `index.html` file with the command `sudo chown www-data:www-data ./index.html`

* Overide the default html file of the Apache Webserver with the command below `sudo cp -f ./index.html /var/www/html/index.html`

* Restart the webservers to load the new configuration using the command below `sudo systemctl restart apache2`

![Alt text](<Images/12.server 1 html created.PNG>) ![Alt text](<Images/12.server 2 html created.PNG>)

- Copy the public IP address of the server and paste is on any web browser of your choice, you should find the page in your browser looking like the below screenshots:

![Alt text](<Images/13.server 1 success.PNG>)

_Server 1_

![Alt text](<Images/13.server 2 success.PNG>)

_Server 2_

### Step 5: Configure Nginx as a Load Balancer

- Create a new EC2 instance running ubuntu 22.04. Ensure port 80 is opened to accept traffic from anywhere

![Alt text](<Images/14.Nginx server.PNG>)

![Alt text](<Images/15.Nginx sec.PNG>)

- Connect to the server via your terminal

- Install Nginx on the server `sudo apt update -y && sudo apt install nginx -y`

![Alt text](<Images/16.Nginx installed.PNG>)

- Verify that Nginx is installed and active `sudo systemctl status nginx`

![Alt text](<Images/17.Nginx active.PNG>)

- Open Nginx configuration file with the command `sudo nano /etc/nginx/conf.d/loadbalancer.conf`

- Paste the below configuration to configure Nginx to act like a load balancer.

```

upstream backend_servers {

    # your are to replace the public IP and Port to that of your webservers
    server 18.234.62.1:8000; # public IP and port for apache webserver 1
    server 54.166.150.195:8000; # public IP and port for apache webserver 2

}

server {
    listen 80;
    server_name 3.80.128.50; # provide your load balancers public IP address

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

![Alt text](<Images/18.Nginx config.PNG>)

- Test your configuration with the command `sudo nginx -t`

- If there are no errors as in the screenshot above then restart Nginx to load the new configuration `sudo systemctl restart nginx`

![Alt text](<Images/19.Nginx codes.PNG>)

- Paste the public IP address of Nginx load balancer and you should see the same webpages served by the apache webservers. In the screenshot below you can see the load balancer serves Apache-Server-1's public IP address.

![Alt text](<Images/20.Nginx server 1.PNG>)

- If you refresh the webpage you will notice that the load balancer serves Apache-Server-2's public IP address as well.

![Alt text](<Images/20.Nginx server 2.PNG>)

## Project on Implementing Load Balancing with Nginx Completed
