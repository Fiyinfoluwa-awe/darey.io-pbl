# IMPLEMENTING A CLIENT-SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

 I created  and configured two Linux-based virtual servers (EC2 instances in AWS)

 `sudo apt update -y`
 
 ![step 1 sudo apt update on server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b4d04522-26a5-4d6b-9566-8c897c7598f8)
 
![step 1b sudo apt update on client ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e32acd10-693a-4e91-8b45-bd4edc74e82f)

On my mysql client Linux server, installed MYSQL Client software

`sudo apt install mysql-client`

![step sudo apt install mysql-client](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/402ac133-e20c-4103-bd0f-f448f673b1d4)

On my mysql server Linux server, installed MYSQL Server software

`sudo apt install mysql-server`

![step 2b sudo apt install mysql-server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4f3dc0e2-7209-4558-91a9-99ca13831902)

Both of the EC2 virtual servers are located on the same local virtual network, so they can communicate with each other using the local IP address. Edited the mysql server's inbound rules, adding a new entry to the security group ; TCP port 3306 by default .For extra security,didn't allow all IP addresses to access my mysql server, allowed access only from my mysql client IP address

![editing the inbound rule of the server ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2d980944-c6cd-43be-afe2-f498a3871485)

Verified the status of mysql by running the command `sudo systemctl status mysql` on my mysql server

![step 3 sudo systemctl status mysql server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ba6d760a-b61f-4983-88de-09a9d20d3b7d)

Configured MySQL server to allow connections from remote hosts.

`sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf`

Replace "bind-address" ‘127.0.0.1’ to ‘0.0.0.0’ like this:

![replacing the bind address 127 0 0 1 to 0 0 0 0](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3264e659-3b8d-4d55-a4d6-7c0bca2a7bb3)

Setting up the mysql database in  Mysql server, so that the client will be able to connect with it, I ran the following commands:

`sudo mysql`

this launched mysql database.

Next I ran the command:

`mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWork.5';`

I exited and then started the interactive script by running:

`sudo mysql_secure_installation`

At the prompt, I put in the password I specified earlier i.e PassWork.5. To create a validated password, type y. Then I chose the strength of my new password I wanted to create at the prompt, I put in the new password.

Type y at the following prompts to validate the changes.

![sudo mysql ALTER USER](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ce303fc1-a5c2-4bd1-bb6e-cfc054cf16cc)

 I ran into few errors, when I was inputting my invisible password, but it worked out at the end of the day!Finished my setup.

![sudo mysql secure installation contd](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9cf21c0f-ede3-482d-bc8f-4ee7276699a3)

When I finished, logged into my MySQL console by typing:

`sudo mysql -p`

I tested the password I created

![testing the password created](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/372f8299-ae2a-48ad-a270-a1bdc82da538)

Created a new database, ran the following commands from my MySQL console:

`mysql> CREATE DATABASE `example_database`;`

I replaced 'example_database' with 'test_database'.

Created a new user and granted the user full privileges on the database I have just created.

`mysql> CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password'`

I replaced 'example_user' with 'dherby' and 'password' with the new password we just created i.e 'Dedo2k3'

To give this user (i.e 'dherby') permission over the 'test_database' database:

`mysql> GRANT ALL ON example_database.* TO 'dherby'@'%';`

Then, I exited the mysql shell.
To test if my new user has the proper permissions by logging in to my MySQL console again, this time using the custom user credentials:

`mysql -u dherby -p`


![creating user and databases ,  grant privileges](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c82b0153-26a1-4e37-93d2-3201014d64a2)

at the prompt, I put in my  password, then run the command:

`mysql> SHOW DATABASES;`

![show databases on server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ad5d1afa-9cdb-414b-9090-eadee35c3ddf)

Then I went to my MySQL client terminal to connect to my MySQL server database engine remotely without using the SSH.

`mysql -u dherby -h <IP-address> -p`

of the Server.

but it didn't connect , had an error. Had to open  and edit the 'vim /etc/mysql/mysql.conf.d/mysqld.cnf' on the server.

`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`

Edited the mysqlx-bind-address = 127.0.0.1  to 0.0.0.0 

![editing vim bind address ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/67c03f1a-f421-4497-adb6-e5baba1f568c)


I retried the connection from the client , and it worked


At the prompt I put in the password.

then ran the command

`mysql> SHOW DATABASES;`

![connected to the server from the client ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/333fe3af-6fa0-48e2-b725-7132146e1ed0)

THE END!



