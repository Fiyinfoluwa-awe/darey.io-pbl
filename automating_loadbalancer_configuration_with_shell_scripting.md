# AUTOMATING LOADBALANCER CONFIGURATION WITH SHELL SCRIPTING 

## Deploying and Configuring the Webservers

The process of deploying our webservers has been codified in the shell script below.

```
#!/bin/bash

####################################################################################################################
##### This automates the installation and configuring of apache webserver to listen on port 8000
##### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
######## ./install_configure_apache.sh 127.0.0.1
####################################################################################################################

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure

PUBLIC_IP=$1

[ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

sudo apt update -y &&  sudo apt install apache2 -y

sudo systemctl status apache2

if [[ $? -eq 0 ]]; then
    sudo chmod 777 /etc/apache2/ports.conf
    echo "Listen 8000" >> /etc/apache2/ports.conf
    sudo chmod 777 -R /etc/apache2/

    sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

fi
sudo chmod 777 -R /var/www/
echo "<!DOCTYPE html>
        <html>
        <head>
            <title>My EC2 Instance</title>
        </head>
        <body>
            <h1>Welcome to my EC2 instance</h1>
            <p>Public IP: "${PUBLIC_IP}"</p>
        </body>
        </html>" > /var/www/html/index.html

sudo systemctl restart apache2
```

#### Provisioned two EC2 instances running on Ubuntu 20.04 for Apache webservers

![step 1 creating instances](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2336d6da-d71c-4be1-993b-311772455a63)

#### Edited the security group's inbound rules, by opening port 8000 to allow traffic from anywhere.

![step 2 editing the inbound balancer](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9b802242-3946-46a7-897f-1197cffbdaf9)

#### connected to the webserver via the terminal using SSH client

![step 3 ssh into apache server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dcbd026e-e455-41f4-840c-2a60e7140d9d)

![step 3 b ssh into apache server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/033048c9-9b88-453c-a8ed-f51ae69df62e)

#### Opened a file (install.sh)  and pasted the above script in both webservers, using the command below;

`sudo vi install.sh` 

![step 4 sudo vi install sh into server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9c9b8a1b-33dc-4a74-9998-a7cd8c37e8ed)

![step 4 b sudo vi install sh into server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/cc928134-c29b-418c-90fd-68cc3d41d91a)

saved and exited

#### Changed the permissions on the file to make it executable using the command below;

`sudo chmod +x install.sh`

confirmed it worked with;    `ls -latr`

![step 5 sudo vi install sh  and chmod of install sh on server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/035faa09-be0d-45f3-888c-b19579efc65f)

![step 5b sudo vi install sh  and chmod of install sh on server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6816aaf0-b54e-4bdb-8068-d8985557aa3c)

#### Ran the shell script using the command below;

`./install.sh PUBLIC_IP`

![step 6  install sh ip address server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5f7c0dc2-e4f5-4c41-8f56-d2dc97297c80)

![step 6b  install sh ip address server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9bd14b76-60cc-4b3a-b435-21b91100fcdf)

Both apache webservers are now serving webpages on the browser displaying their public IP addresses

![step 7 apache server 1 showing ip address](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0a4f540a-642d-41d3-b205-5b7a4a9e22d3)

![step 7b apache server 2 showing ip address](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/cc20d917-d336-430e-a265-9ff2a03789a8)

## Deploying and Configuring the Nginx Loadbalancer

Provisioned another EC2 instance running ubuntu 22.04, opened port 80 to anywhere using security group and connected to the load balancer via the terminal.

![step 8 creation of nginx ec2 instamce](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/72b33b0a-cfee-4c28-8f45-1fcfc2fa50fb)

![step 9 editing the inbound rule of nginx](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/48173763-e8de-4337-9b31-cb9bca79fced)

![step 10 ssh into Nginx server ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/de5bdf98-3792-4028-926d-72ea9636e2c5)

Below is the script to be used for implementing the loadbalancer with Nginx , which has been codified;

```
#!/bin/bash

######################################################################################################################
##### This automates the configuration of Nginx to act as a load balancer
##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
##### the webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
##### ./configure_nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
#####  ./configure_nginx_loadbalancer.sh 127.0.0.1 192.2.4.6:8000  192.32.5.8:8000
############################################################################################################# 

PUBLIC_IP=$1
firstWebserver=$2
secondWebserver=$3

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # your are to replace the public IP and Port to that of your webservers
            server  "${firstWebserver}"; # public IP and port for webserser 1
            server "${secondWebserver}"; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name "${PUBLIC_IP}";

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi

sudo nginx -t

sudo systemctl restart nginx
```

On my terminal, opened a file 'nginx.sh' using the command;

`sudo vi nginx.sh`

#### Copied and pasted the above script inside the file

![step 11 sudo vi nginx sh](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/83192d2e-e5b6-47ac-8cc2-82e757a91aa5)

Saved and closed the file. Type "esc" then ":wqa!"

#### Change the permission of the file to make it an executable file using the command;

`sudo chmod +x nginx.sh`

#### Ran the script with the command;
 
 `./nginx.sh PUBLIC_IP Webserver-1 Webserver-2`
 

![step 12 chmod nginx run ip addresses](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f34979b8-0503-469e-96c1-9eb9e3f6984a)

![step 13  nginx sh ip addresses reult](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2c31503a-9d55-4db8-8161-2c5859abbc09)

Tried to check the loadbalancer, but got the 504 error;


![browser error 504](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ade40bf0-6d95-4845-a879-ea9c9004bb55)

#### Edited the command used to run the shell script, including the port 8000 for the apache servers;

`./nginx.sh PUBLIC_IP Webserver-1 Webserver-2`

![error correct editing the ip addresses and including the ports of the apache servers](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/25d251f9-4e00-4434-ae63-a52c52f05297)

#### Did the browser check for loadbalancer again, it worked;

![nginx sever browser check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e858d6f6-19b4-4272-9340-0c4b0135ba43)

![nginx sever browser check for the other apache  server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8bf281cb-0530-430b-ba85-7f924bb50520)















