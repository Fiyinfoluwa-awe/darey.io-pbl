# Implementing WordPress Website with LVM Storage Management

![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/11720c26-64e3-4a1b-b8c1-9117945526ae)

WordPress is a web content management system. It was originally created as a tool to publish blogs but has evolved to support publishing other web content, including more traditional websites, mailing lists and internet forums, media galleries, membership sites, learning management systems and online stores. WordPress is among the most popular content management systems and is written in PHP hypertext preprocessor language and paired with a MySQL or MariaDB as its backend Relational Data Management System (RDBMS).

The project will be broken into two parts

Configure storage subsystems for Web and Database servers based on Linux OS. This will give us practical experience of working with disks, partitions and volumes in Linux.

Install WordPress and connect it to a remote MySQL database server. This will help solidify your skills of deploying Web and DB tiers of web solutions.

Three-tier Architecture
Generally, web or mobile solutions are implemented based on what is called the Three-tier Architecture. It is a client-server architecture pattern that comprises of three(3) separate layers.

Presentation Layer: This is the user interface such as the browser on your laptop.

Business Layer: This is the backend program that implements business logic. Application or webserver.

Data Access or Management Layer: This is the layer for computer data storage and data access. Database Server or File System Server such as FTP Server or NFS Server.

In this project we will showcase Three-tier Architecture while also ensuring that the disks used to store files on the linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

Our 3-Tier setup will consist of:

A laptop or PC to serve as a client.
An EC2 Linux Server as a web server (Where we will be installing WordPress).
An EC2 Linux Server as a database (DB) server.
We will be using RedHat OS for this project

Implementing LVM on Linux Servers (Web and Database Servers)

## Starting the Project
I launched an EC2 instance that will serve as "Web Server" and create 3 10GiB volumes in the same Availability Zone (AZ) as your web server, which I attached to my Webserver.

![step 1 creating Red hat EC2 instances ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/011a7afc-ee3b-4f7a-a10d-7cba8bd4ae2e)

![step 2 three volumes created ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/669aff95-42c3-4ff3-a6f6-b43e45bb1338)

Opened up my linux terminal, ssh into my instance, to begin configuration

Used `lsblk` command to inspect what block devices are attached to the webserver. All devices in Linux reside in /dev/ directory. Inspected this directory using the command `ls /dev/` to confirm  all 3 newly created block devices there.

Their names are be xvdf, xvdh, xvdg.

![step 1 ssh into redhat instance ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5fbfae91-3524-4d1c-907c-0b7206162cc5)

![step 2 lsblk to inspect blocks attached to instance](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6c7a18ef-d059-4a50-a3db-cb0e5d912a76)

![step 3 ls dev](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9dfa1301-7160-4eae-adf6-0703c3518fe6)

Used gdisk utility to create a single partition on each of the 3 disks using the command 

```
sudo gdisk /dev/xvdf 
sudo gdisk /dev/xvdg
sudo gdisk /dev/xvdh
```

![step 5 sudo gdisk xvdf](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/de479672-36d2-47fe-b081-9ab9c923e286)

Used `lsblk' utility to view the newly configured partition on each of the 3 disks.

![step 6 lsblk to view newly  configured partitions](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c3bea642-f7c5-4538-ae26-9eef29b381d7)

 Installed LVM2 package using the command `sudo yum install lvm2`. Then ran `sudo lvmdiskscan` command to check for available partitions.

![step 7 sudo yum install lvm2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e01c301d-28dc-46f5-bdf4-44d40d976111)

![step 8 checking for available partitions](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/326d2f03-0907-480d-9886-9c40c02a1f95)

Used pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

![step 9 using pvcreate utility to create physical volumes ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c3d45b9c-38a9-43f3-9aed-04b05175f40b)

Verified that the physical volumes have been created successfully by running `sudo pvs`

![step 10 verifying with sudo pvs that physical volumes have been created](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6236dcc1-5de9-4326-aa05-e7f7e39d8684)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

`sudo vgcreate webdat-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

and verified with `sudo vgs`

![step 11 creating a volume group and verifying that it was created](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2b618ee7-65ce-4c01-91c1-262c30ea589c)

Used lvcreate utility to create 2 logical volumes. apps-lv and logs-lv which will be used to store website data and log data respectively.

```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
and verified that it was created with `sudo lvs`

![step 12 creating logical volumes and verifying that it was created](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b057f0d2-7f46-4573-af4e-b426b46e691a)

Verified the entire setup with;

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![step 13 sudo view vgdisplay](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1d926d27-3fb6-46c3-8046-14a18c2412eb)

![step 14 sudo lsblk](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/970ecc52-c6a6-4cff-a1bd-2ece66868c5a)


Used mkfs.ext4 to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

![step 15 using mkfas ext4 to format the logical volumes](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/89074491-a18b-475a-a30f-1d8a586bd49b)

Created /var/www/thml directory to store website files

`sudo mkdir -p /var/www/html`

Created /home/recovery/logs to store backup of log data

`sudo mkdir -p /home/recovery/logs`

Mounted /var/www/html on apps-lv logical volume

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

`sudo rsync -av /var/log/. /home/recovery/logs/`

![step 15 b mouting var www html on appslv](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d55a18ec-24e5-472d-b458-ab051123ecee)


![step 16 using rsync utility to bcakup files in logs ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ea24e66b-b49e-4640-af65-8b70ddf67a74)

 Mounted /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)

`sudo mount /dev/webdata-vg/logs-lv /var/log`

Restored log files back into /var/logs directory

`sudo rsync -av /home/recovery/logs/. /var/log`

![step 17 mounting var log on logs lv and restoring the backuped files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e0f91f99-232b-4e81-864a-92c69827536f)


Updated /etc/fstab file so that the mount configuration will persist after restart of the server. The UUID of the device will be used to update the /etc/fstab file;

`sudo blkid`

![step 18 sudo blkid ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/658ea4af-0b97-4bbe-a489-91b1317ff5a2)


Update /etc/fstab inputing the UUID of the logical volumes,remove the leading and ending quotes (').

`sudo vi /etc/fstab`

![step 19 updating the etc fstab inputing my UUId ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/24a3ee6e-b4fc-481e-94a0-4520a914c40d)

Tested the configuration for any errors and reloaded the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

Verified the setup by running `df -h`.

![step 20 testing for errrors , reloading df -h](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2b99431e-6713-4ead-a933-d3343710c5c2)


## Installing WordPress and configuring it to use MySQL Database

Prepared the Database Server, Launched a second RedHat EC2 instance that will have a role - 'DB Server'. Repeat the same steps as for the webserver but instead of 'apps-lv', create 'db-lv' and mount it to /db directory instead of /var/www/html/.

See below screenshots of implementation for the DBServer instance using LVM-SM

Open up the linux terminal to begin configuration.

Used `lsblk` command to inspect what block devices are attached to the webserver. All devices in Linux reside in /dev/ directory. Inspect this directory using the command ls /dev/ and confirm you see all 3 newly created block devices there.

_Their names will likely be xvdf, xvdh, xvdg.

Used `df -h` command to see all mounts and free space on your server.

![volemes created for database servers and attacjed ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a10c4880-0c5f-4c2e-8c63-b9e0baabbcf4)

![step 1 lsblk gdisk](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ad026de2-7b9b-4792-ae78-7aa0dd733d7a)


Used gdisk utility to create a single partition on each of the 3 disks using the command `sudo gdisk /dev/xvdf`

Used lsblk utility to view the newly configured partition on each of the 3 disks.

![step 2 sudo gdisk ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c0a10545-246f-4455-98f2-2087b6481e68)


Installed LVM2 package using the command `sudo yum install lvm2`. Ran `sudo lvmdiskscan` command to check for available partitions.

Used pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

Verified that the physical volume has been created successfully by running `sudo pvs`

Used vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG 'webdata-vg'

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

Verified that the VG created successfully by running the command `sudo vgs`

Used lvcreate utility to create 2 logical volumes. 'db-lv' and 'logs-lv' which will be used to store website data and log data respectively.

```
sudo lvcreate -n db-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

Step 12: Verify that your Logical Volume has been created successfully by running `sudo lvs`

![step 3 install lvm2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ea7df57d-7f2b-4fb1-ad69-3ea224a55799)

![step 4 lvcreate vgcreate pvcreate ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bf0eeb90-462f-4036-9cd7-18c933d6873d)

I created 'apps-lv' instead of 'db-lv', which I rectified, as seen below, with the command `sudo lvremove /dev/webdata-vg/apps-lv`

Verified the entire setup, using this command;

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![step 5 vg display - complete display](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f0fc0ca7-854a-4e5b-a21d-3eaaece6ff87)

Used mkfs.ext4 to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/webdata-vg/db-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

Created /db directory to store website files

`sudo mkdir -p /db`

Created /home/recovery/logs to store backup of log data

`sudo mkdir -p /home/recovery/logs`

Mount /db on db-lv logical volume

`sudo mount /dev/webdata-vg/db-lv /db/`

Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

`sudo rsync -av /var/log/. /home/recovery/logs/`

Mounted /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is backing up the log files with rsync utility  is very important)

`sudo mount /dev/webdata-vg/logs-lv /var/log`

Restored log files back into /var/logs directory

`sudo rsync -av /home/recovery/logs/. /var/log`

![step 6 mounting rsync ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ae6788ac-c737-4932-bee2-e3bdfc08662c)

Updated /etc/fstab file so that the mount configuration will persist after restart of the server. The UUID of the device will be used to update the /etc/fstab file;

```
sudo blkid
sudo vi /etc/fstab
```

![step 7a sudo blkid getting the uuid of the device](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a03c86c8-3c95-440e-b9fd-d4ff37665615)

Updated /etc/fstab in this format using your own UUID and remember to remove the leading and ending quotes.

![step 7 editing the etc fstab file ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/227bf57f-c3fb-47c9-a14c-7e0166ffdc4e)

Tested the configuration and reloaded the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

Verified the setup by running `df -h`. The output will look like in the below screenshot.

![step 8 sudo mount a  for any error reload df -h](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dac7d431-1247-4324-b8d5-fc3ec7152e8e)


## Installing Wordpress on the Webserver EC2 Instance

Updated the repository 

`sudo yum -y update`

![step 21 running sudo -y update on webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c67e7392-757d-4d07-9a6c-0ef3a6877489)

Installed wget, Apache and it's dependencies 

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![step 22 installing wget apache and its dependies on the webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5f7474f3-45d8-4344-9825-71104aa2cb32)

Start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```
![step 23 starting and enabling apche on the webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/47db3d1b-948d-484c-9a61-0ac42d0b454c)

Installed PHP and it's dependencies

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1
Restart Apache sudo systemctl restart httpd
```

![step 24 sudo yum fedora](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/019d1eab-706e-4a4e-b254-235cb086ffce)
![step 25 yum utils ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d37e9b4d-9821-492d-9193-99abc62d5f34)
![step 26 yum php etc](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c1c666d5-cd55-4ae7-88b8-3dcb82e7edf1)
![step 27 start php ans set policies , restart apache ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/af1315fc-3287-4c60-9139-7d526091d3a9)
![step 27 start php ans set policies](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e9af3d9e-cf44-4340-ae74-1aeda258925d)

Downloaded wordpress and copy wordpress to /var/www/html

```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
sudo cp -R wordpress /var/www/html/
```

![step 29 create and cd into wordpress directory](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/825d9098-a495-4527-9c0f-87482a3b5a0a)
![step 30 download wordpress file ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/454eb1b1-4202-4a45-8f46-c3f4e55c41a2)
![step 31 unzip wordpres files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/00299bac-4887-4d46-a288-a0ce98cbdaa0)
![step 32 wordpresss cop var html ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/71954b49-64d9-4eb0-9b90-4c613458f6bc)
![step 32 wordpresss cop var html ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/15a724bf-e497-4ea8-b1c2-fd5d1f28721d)
![step 33 ls var ww html ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a53869f4-ec00-4715-b577-de284262b90c)


Configured SE Linux Policies

```
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```
![step 34 configure SELinux Policies](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f5617113-74f1-40ed-808c-7aa131536282)

Restarted Apache

 `sudo systemctl restart httpd`

 Copied the IP address of the webserver to the browser to see that the apache is working properly

![step p29browser check for the webserver ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f52143b6-c823-466c-bb6b-0093b8786487)

## Installing MySQL on the DB Server EC2 Instance

Installing MySQL on the DB Server EC2 Instance

```
sudo yum update
sudo yum install mysql-server
```
![step 9 sudo yum update](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/06bbf761-fa8e-4398-87e3-4295d7a460b5)

![step 10 sudo yum install mysql-server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1f49a5d2-b3ea-4a2d-a6dd-9e76a9de2f02)

Verified the service is up and running using `sudo systemctl status mysqld`. If it is not runnning, restart the service and enable it so it will be running even after reboot.

```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

![step 36 sudo restart mysqld , enable ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f93efd9c-5c83-449f-b45a-5fbaa34d5006)


Configured Database server to work with WordPress; for the creating the user inputted the private ip address of the webserver

```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER 'ec2-user'@`172.31.38.33` IDENTIFIED BY 'password';
GRANT ALL ON wordpress.* TO 'ec2-user'@'172.31.38.33';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

![step 11 configuring mysql ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/673d6e33-eccb-4f22-a8be-5172a1b44ead)

## Configured WordPress to connect to remote database

Opened MySQL port `3306` on DB Server EC2. For extra security, we will allow access to the DB server ONLY from our Webserver's IP address, so in the inbound rule configuration specify source as /32

![editing inbound rule](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0d426521-ef63-4706-9b50-813d86711a3b)

Installed MySQL client and tested that we can connect from our webserver to our DB server by using mysql-client with `sudo mysql -u 'username' -p -h 'ip_database_server'

```
sudo yum install mysql
sudo mysql -u ec2-user -p -h 172.31.41.10
```
Verified that I can successfully execute SHOW DATABASES; command and see a list of existing databases.

![step 40 installling mysql -client on webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5af0e473-5b3b-4456-989f-aa8abaee1872)
![step 41 connect to mysql from client webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/558c5e43-7600-469e-ac16-64918711f72d)

Changed permissions and configuration so Apache could use WordPress

![configure SELinux Policies](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7a35f167-5162-499a-b2b9-8b6ca9cd0c4a)
![step 44 apache owns the files ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/76e514d7-100d-4373-8fe7-eb47b46da445)

Enabled TCP port 80 Inbound Rules configuration for your Web Server EC2 (Enable from everywhere 0.0.0.0/0 or from your workstation's IP)

![adding http to the inbound rule of the webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/91843ff1-528a-4acf-8b96-81a24d23283c)

Edited the "/var/www/html/wp-config.php" to connect to dataabase. Inputted the database name , the user created , the private ip of the database server

![step 12 editing wop config php file ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9617d2a5-b499-45d4-9d25-524bcf3536aa)

Disabled the apache configuration(i.e renaming to backup)

`mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup`

Opened "/etc/my.cnf" and edited the file. Added [mysqld] and "bind-address=0.0.0.0".

`sudo vi /etc/my.cnf`

![step 13 editing etc my cnf](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/503fb035-ef25-4fe2-a803-9761217c298c)

Tried to access from your browser the link to your WordPress

`http://<Web-Server-Public-IP-Address>/wordpress/`

![browser check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e8c07386-e9e0-4c97-9eb6-06f2182161b4)
![browser check 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c047a87c-8e29-46cd-b032-144446c380a0)
![browser check 3](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7aa9a628-8918-4467-bae9-d2cef3f15ef7)
![step 14 broswer check again](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e7c11ce3-14a5-4293-8dc1-9d0d78b6fd9b)
![wordpresss](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d6b3adaf-deab-483e-9986-ef0f130b5ceb)

#### Implementation done 






