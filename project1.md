# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

## STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL
command - sudo apt update 
![installing Apache ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f2795d06-2735-4205-aca0-f3fa4d49a5f0)

### verifying that apache2 is running as a Service in our OS
command -sudo systemctl status apache2

![continuation of Apache installation 3](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5419a536-d95f-47d8-9635-63960dedf31b)

### Testing how our Apache HTTP server can respond to requests from the Internet.
![browser connection works](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8453aa22-4620-4206-9a43-266f4763d750)

## STEP 2 — INSTALLING MYSQL
command - sudo apt install mysql-server

![installing mysql](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0555e008-b987-4ed3-b08c-d2eccb6547ae)

 log in to the MySQL console 
 command - sudo mysql
 ![logging into mysql console](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c1183998-c1e1-400f-ad52-375295d55f8c)

 Start the interactive script
 command - sudo mysql_secure_installation
 ![mysql secure installation](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/cd0c8f8e-9c2b-470f-8dfa-11ac735de2df)

 testing if I'm able to log in to the MySQL console

 ![testing Mysql console](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/aa26ef0d-30ad-4e57-a7b9-8405aba64baf)

 ## STEP 3 — INSTALLING PHP
 command - sudo apt install php libapache2-mod-php php-mysql
 
![installing PHP](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/da0e863a-e345-4e4c-a5d1-ac5f32e60db4)

confirm your PHP version

command - php -v

![confirming the version of the PHP](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e0a7bc5c-f855-4b57-b872-bb8716dc95db)

## STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE
Creating the directory for projectlamp 
command -sudo mkdir /var/www/projectlamp
![creating virtual host using apache ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3f869615-1ca9-410e-8dd7-8697817b2a97)

assigning ownership of the directory with my current system user
command - sudo chown -R $USER:$USER /var/www/projectlamp

![Assigning ownershio to the directory](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1bb69c29-b12c-4636-8dc5-44f279963132)

 creating and opening a new configuration file in Apache’s sites-available directory 
 
 command - sudo vi /etc/apache2/sites-available/projectlamp.conf
 ![creating a new configuration file in Apache](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8b38e891-29b5-4668-b4d5-e724713a2d2a)

 use the ls command to show the new file in the sites-available directory
 command - sudo ls /etc/apache2/sites-available
![using the ls command to show new file in the sites-available directory](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/04d1ba37-2c17-4c40-8c1f-09c490e3cbeb)

enabling the new virtual host
command - sudo a2ensite projectlamp

sudo a2dissite 000-default

sudo apache2ctl configtest

sudo systemctl reload apache2

![enabling virtual host](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/21bf656b-62de-408c-a03d-29e339b7f1a0)

![reload apache ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bcf72d8d-3281-48e6-b7e0-624a4220bf07)

checking website URL on browser using IP address
![refreshing browser](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/817f96a6-aa8c-4b57-b13c-a97c99df4836)

## STEP 5 — ENABLE PHP ON THE WEBSITE
command - sudo vim /etc/apache2/mods-enabled/dir.conf

<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

![Enabling PHP on the website](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4f120e6e-9e48-41a5-8c6e-634d68b28d73)

reload apache 
command - sudo systemctl reload apache2
Create a new file named index.php inside your custom web root folder
command - vim /var/www/projectlamp/index.php

![completed Project 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/29e41796-55b6-4b56-b8aa-72dc42954559)

refreshing browser page 
![refreshed browser after enabling php](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2f94a2cc-4d91-4f7f-9740-3c74dc05103f)

Removing sensitive information on browser page 
command - sudo rm /var/www/projectlamp/index.php
![refreshed page at the end of project1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6bdcd68f-c0cf-47ce-a2a4-d8ed8022e175)
