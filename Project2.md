# WEB STACK IMPLEMENTATION (LEMP STACK)

## STEP 1 – INSTALLING THE NGINX WEB SERVER
commands - `sudo apt update`

![installing Nginx webserver_ sudo update](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4b803a46-52ab-44ab-b341-dc84d8e43b70)

 command - `sudo apt install nginx`
 
![installing Nginx webserver_ sudo apt install](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d0b890f5-766e-47a7-b36a-38b04178fa71)

Verifying that nginx was successfully installed and is running as a service in Ubuntu, 
command - `sudo systemctl status nginx`

![verifying that nginx was successfully installed ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7a745638-0ff2-4975-ace7-a45b23360ad9)

First try to check if the server locally in our Ubuntu shell (browser check)

![Browser check 1 after editing inbound rule](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e3cbd1e4-00e7-4c51-86a0-333022fbd750)

## STEP 2 — INSTALLING MYSQL
 command - `sudo apt install mysql-server`

![installing MYSQL sudo apt install msql server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/99658d01-92fd-4e90-96da-e979fe222eee) 

When the installation is finished, log in to the MySQL console 
command - `sudo mysql`

![logging into msql console](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/692d5e9a-bab2-47d7-bad6-b67868b344b9)

## STEP 3 – INSTALLING PHP
`sudo apt install php-fpm php-mysql`
![installing php sudo apt install php fpm php mysql](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/244edc7b-e24d-48b3-a03e-37dbad045fb8)

## STEP 4 — CONFIGURING NGINX TO USE PHP PROCESSOR
Create the root web directory for your_domain 

command - `sudo mkdir /var/www/projectLEMP`

assigning ownership of the directory with the $USER environment variable, which will reference your current system user:

command - `sudo chown -R $USER:$USER /var/www/projectLEMP`

![assigning ownwership of directory](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9a68a6ea-3433-4d33-8205-499d523377d0)

Opening a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, I used nano:
command - `sudo nano /etc/nginx/sites-available/projectLEMP`

![opening new config file in nginx site](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6763cb08-57a5-4918-9915-03f780f6e872)

This created a new blank file and pasted in the following bare-bones configuration, in it.
command -
```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

![barebone nano](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/515340ac-c5be-4a0a-a4e0-7a2fd62b904d)

Activating my configuration by linking to the config file from Nginx’s sites-enabled directory:

command - `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

Testing my configuration for syntax errors

command - `sudo nginx -t`


![activating configuration by linking the config file and config test](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/52454071-5eb4-49e9-be44-665473fd2eee)

Disabling default Nginx host that is currently configured to listen on port 80

command - `sudo unlink /etc/nginx/sites-enabled/default`

reload Nginx to apply the changes

command - `sudo systemctl reload nginx`

![disable nginx host reload nginx creating index html](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/df069757-0818-4531-9955-989a130e0dcb)

Creating an index.html file in that location so that we can test that your new server block works as expected
command -
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```

Going to my browser to open my website URL using IP address


![browser check 2 after creating index html command](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d22f5651-05e0-4a13-8288-c474b3f79e8f)

## STEP 5 – TESTING PHP WITH NGINX

Creating a test PHP file in my document root. Open a new file called info.php within your document root in my text editor:
command - `sudo nano /var/www/projectLEMP/info.php`

![cat info php](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/06b6c4d4-53d0-4efd-8e4c-4a7fe97305db)

Accessing this page in my web browser by visiting the domain name or public IP address I’ve set up in my Nginx configuration file, followed by /info.php:

![Broswer check 3 info php](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c57815ef-e47f-4a89-a3bb-5aadf1a5bf53)

Removing sensitive information using command 
`sudo rm /var/www/your_domain/info.php`

Browser check after running that command

![browser check 3 after removal of the sensitive info](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7517b5cb-bd15-431b-a212-63c2ad0811c4)

## STEP 6 – RETRIEVING DATA FROM MYSQL DATABASE WITH PHP (CONTINUED)

 Connecting to the MySQL console using the root account

 Commands -
 
 ```
 sudo mysql
 mysql> CREATE DATABASE `example_database`;
 mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
 mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
 mysql> exit
 ```

 ![retrieving data from mysql database , creating database](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/cf8bca22-806f-419b-97d8-3e38a797a3ab)

Testing if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

commands - 
```
mysql -u example_user -p
mysql> SHOW DATABASES;
```

Creating a test table named todo_list. From the MySQL console,
Command - `CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));`
Inserting a few rows of content in the test table
 command - `mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

To confirm that the data was successfully saved to your table, run:
Command - `mysql>  SELECT * FROM example_database.todo_list;`
`mysql> exit`

![creating database last step](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c6822b2a-3d9e-4066-9b6c-0f490250ebe2)

Now I created a PHP script that will connect to MySQL and query for your content. Creating a new PHP file in my custom web root directory using my preferred editor. I used vi for that:
command - `nano /var/www/projectLEMP/todo_list.php`

![creating php script](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f8ad357e-2cec-4d7c-8ffb-860c809896a4)

![end of project 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2d5597b6-cc70-4a6e-a31d-6ed5fa240d6d)

Broswer check, showing the content I’ve inserted in my test table:,

![browser check at the end of project 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8c8d7a66-9655-4d78-b74c-ea59aa778818)


