# DevOps Tooling Website Solution Project

In this project, I will implement a tooling website solution making access to DevOps tools within the corporate infrastructure easily accessible. My solution will consist of the following components.

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. Storage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub

## Implementing a business website using NFS for the backend file storage

### Preparing the NFS Server
Spined up a new EC2 instance with RHEL Linux 8 Operating System.
Based on my LVM experience from my Wordpress Project, Configured LVM on the Server.

Instead of formatting the disks as ext4, I formatted them as xfs

Ensured there are 3 logical volumes; lv-opt, lv-apps and lv-logs.

Created mount points on /mnt directory for the logical volumes as follows'

##### Mount lv-apps on /mnt/apps - To be used by the webservers
##### Mount lv-logs on /mnt/logs - To be used by webserver logs
##### Mount lv-opt on /mnt/opt - To be used by Jenkins server

created gdisk utility to create partitions for each of the added volumes

`sudo gdisk /dev/xvd[f/g/h]`

Installed LVM2 package using the command `sudo yum install lvm2`.

Ran `sudo lvmdiskscan` command to check for available partitions.

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

Verified that the physical volumes hassuccessfully been created by running `sudo pvs`

Used vgcreate utility to add all 3 PVs to a volume group (VG). Named the VG; `webdata-vg`

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

Verified that the VG created successfully by running the command `sudo vgs`

Used lvcreate utility to create 3 logical volumes;lv-apps, lv-logs and lv-opt, which will beused to store website data, log data and jenkins data respectively.

```
sudo lvcreate -n lv-apps -L 9G webdata-vg
sudo lvcreate -n lv-logs -L 9G webdata-vg
sudo lvcreate -n lv-opt -L 9G webdata-vg
```
Verified that the logical volumes has been successfully created  by running `sudo lvs`

Verified the entire setup, by running this command;

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![step 1 lsblk vgdisplay](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eba3a3c5-eab2-4f93-a694-0c6dbab052a7)

Used mkfs.xfs to format the logical volumes with 'xfs filesystem', ran these commands;

```
sudo mkfs -t xfs /dev/webdata-vg/lv-apps
sudo mkfs -t xfs /dev/webdata-vg/lv-logs
sudo mkfs -t xfs /dev/webdata-vg/lv-opt
```
![step 2 formatting with xfs ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b1ec4cae-c279-496e-83e3-98cdb0592d6d)

Created directories to mount the logical volumes, with these commands;

```
sudo mkdir -p /mnt/apps
sudo mkdir -p /mnt/logs
sudo mkdir -p /mnt/opt
```

Mounted the logical drives unto the respective directories.

```
sudo mount /dev/webdata-vg/lv-apps /mnt/apps
sudo mount /dev/webdata-vg/lv-opt /mnt/logs
sudo mount /dev/webdata-vg/lv-logs /mnt/opt
```

![step 3 making direction and mounting the logical volumes](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/03c78679-b60d-47cf-992e-b73506f52ba8)

ran `lsblk' to view the mounted files

![step 5 view the mounta](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/74121a7e-8575-4019-800a-3784b28aab45)

Tested the configuration by running this command below, to ensure there are no errors, and  if everything is ok.

`sudo mount -a`

Reloaded the daemon, with;

`sudo systemctl daemon-reload`

Verified the setup by running;

`df -h`

![step 7 verifying the mount output](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ad94079d-1a2c-4075-9645-996eb460deb4)


## Update /etc/fstab

Updated the /etc/fstab in this format, inputting the UUID of the device, remembered to remove the leading and ending quotes(').

ran `blkid` to get the UUID of the device, 

Opened the file with `sudo vi /etc/fstab`

![step 6 editinf the etc fstab file ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/03433a4e-d4c1-43cc-89e2-8717a52a2f12)

Installed  NFS server, configured it to start on reboot, and also made sure  that it is up and running. Ran these commands;

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

![step 4](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eea12992-0b0f-40d8-b500-6bce36f6e051)
![step 8 sudo yum update and install nfs ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/41b6a70c-8d5e-45d8-a9ff-0917a526a18a)
![step 9 start enable status nfs](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6e7a6791-8f00-4f60-a47c-3e6623fa57bb)

Exported the mounts for webserver's subnet cidr to connect as clients. For simplicity, I will be installing all 3 webservers inside the same subnet but in production setup, I would probably want to seperate each tier inside it's own subnet for higher level of security. To check your subnet cidr, open your EC2 details in AWS web console and locate the Networking tab and open a subnet link.

Ensured to set up permissions that will allow the Web servers to read, write and execute files on NFS:

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```

![step 12 chmod chown of apps logs opt](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/24afc946-b4b6-405b-824f-d51abe2a815e)

Configured access to NFS for clients within the same subnet (example of subnet CIDR - 172.31.32.0/20)

Opened the '/etc/exports'file , using;

`sudo vi /etc/exports`

and inputing the below format in it;

```
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
```
Saved the file with `wq!`


![step 13 sudo vi etc exports](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/168e8ee1-f0a6-43e9-8e2a-06fd95d212ff)

After ran `sudo exportfs -arv`

![step 14 exporting the cidr ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/58a368dd-cec7-4cae-bff6-90913930a4bc)

Checked which ports were being used by NFS, and opened it using Security Groups (adding new Inbound Rules)

`rpcinfo -p | grep nfs`

Important note: In order for NFS server to be accessible from the client, I also opened the following ports:

TCP 111
UDP 111
UDP 2049

![step 15 exporting the cidr  and checking ports used by nfs](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9094b99c-a218-47b1-aac4-e9c41cd84df9)
![step 16 editing the NFS inbound rule](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/68f01ed2-e3fd-4aaa-b67d-cde1af67ff82)
![step 16b edited inbound rule of NFS server ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f0e6ac5a-bd88-4174-85c6-8b3aba21dfd4)

## Configuring the backend database as part of 3 tier architecture
Configured the database server. I have to install and configure a MySQL DBMS to work with the remote webserver.
Installed the  MySQL server on Ubuntu Linux OS, ran ;

```
sudo apt update
sudo apt install mysql-server
```

Created a database, which I named `tooling`

Created a 'database user' and named it "webaccess"

Granted permission to "webaccess" user on "tooling" database to do anything only from the webserver's subnet cidr which in this case is 172.31.32.0/20

```
sudo mysql -p password
CREATE DATABASE tooling;
CREATE USER 'webaccess'@'172.31.16.0/20' IDENTIFIED BY 'password';
GRANT ALL ON tooling.* TO 'webaccess'@'172.31.16.0/20';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

![step 10 configuring mysql consol crreating database tooling](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eba8ebfc-cdd9-4318-9b1f-79a26f528a5d)

![step 11 done configuring the mysql , creating user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eefc522f-528e-4379-a7c5-6c78974acf4d)

## Preparing the Web Servers

We need to make sure that our web servers can serve the same content from shared storage solutions. In our case - NFS Server and MySQL database. We already know that one DB can be accessed for reads and writes by multiple clients. For storing files that our web servers will use, we will utilize NFS and mount previously created logical volume lv-apps to the folder where Apache stores files to be served to the users /var/www

This approach will make our web servers stateless which means we will be able to add new ones or remove them whenever we need to and the integrity of the data (in the database and on the NFS) will be preserved. In this step we will do the following:

Configured the  NFS client (this step must be done on all 3 webservers).
Deployed a Tooling application to our webservers into a shared NFS folder.
Configured the web servers to work with a single MySQL database.
Launched 3 new EC2 instances with RHEL 8 OS to serve as the webservers

Installed NFS client,by running this;

`sudo yum install nfs-utils nfs4-acl-tools -y`


![step 17 installing nfs client on webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0a45072b-45e2-48f2-aaf1-484a81c117a0)

Mounted /var/www/ and targeted the NFS server's export for apps

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```

Verified that NFS was mounted successfully by running `df -h`. 

![step 18 mounting var www  and target NFS server's export for apps](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9e28a065-0a9c-42f9-9d20-1c968d2d1314)

Made sure that the changes will persist on web server after reboot, by inputing the UUID of the device in the '/etc/fstab' file

`sudo vi /etc/fstab`

added the following line:

```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
<NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0
```

![step 21 editing the log path in the etc fstab](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/98445bab-4c2d-48c8-ad63-5a7be083c403)

Then installed Remi's Repository, Apache and PHP, by running the folllowing commands;

```
sudo yum install httpd -y
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo setsebool -P httpd_execmem 1
```
![step 20 installing apache on webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/61c5982c-88eb-40a2-9a53-216a8746b462)

![step 23 installing php and dependencies](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2ab8aecb-160b-45a6-8899-e19ffd4010e9)

![step 23b installing php and dependencies contd1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/36c1b6be-28d4-429e-a81d-725b3e5c4cea)

![step 23c installing php and dependencies contd2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/12b85910-6cfd-4346-876b-fe11849d0d51)

![step 23d installing php and dependencies contd3](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/362ce11b-b25b-4855-8115-13106f681c91)

![step 23e installing php and dependencies contd4](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c4e16390-da56-47f5-b49a-429e8b2224e4)

Verified that Apache files and directories are available on the Web Server in '/var/www/' and also on the NFS server in '/mnt/apps'. If you see the same files, it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from the other webservers.

![step 25 deploying the html folder from th repository to var www html](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a4d4ada1-7a5f-4fc0-a991-b75c4cbe87d4)

Located the log folder for Apache on the web server and mounted it to NFS server's export for logs. 

`sudo mount -t nfs -o rw,nosuid 172.31.16.71:/mnt/logs /var/log/httpd`

To make sure the mount point will persist after reboot.

```
sudo vi /etc/fstab
172.31.38.105:/mnt/logs /var/log/httpd defaults 0 0
```

![step 19 editing the etc fstab to for mount to persist on the webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e7bb23fc-dc65-48a9-93d4-a1dea70436f1)

![step 21 mounting logs](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d5190ce8-8dfd-4ffc-913f-3629fa90e762)

I forked the tooling source code from `Darey.io Github Account` to my GitHub account.

![forked from Darey github account](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b5c4be5a-5739-4256-aeb2-5777890b0c2b)

Deployed the tooling website code to the webserver. Ensured that the html folder from the repository is deployed to /var/www/html

First installed git 

`sudo apt install git -y`


![step 23f installing git on webserver](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/fc86c287-259e-4c6c-b4d5-8c11b374b7fb)

Initiated git

`git init`

Copied the url from My Github Account

`git clone < the_url>`

![step 24 git init git clone](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b2c3ae44-c7a6-4222-a6d0-c6fc41745f7b)

Inside the tooling-repo directory, copied  the content of the repository into the directory by running the command:

`git clone <github-url>`

"github-url" is the url I copied from github.

![the code of the tooling](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dda06247-cfd7-4d3f-86f1-c5c26250d0da)

Deployed the html directory to the '/var/www/html' directory.

`sudo cp -R html/. /var/www/html`

![step 25 deploying the html folder from th repository to var www html](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4f3faea9-968c-4206-b361-4a066d054eb0)

Restarted the Apache

`sudo systemctl restart httpd`

Opened port 80 on webservers to be able to connect to the browser.

Opened port 3306 on both database server and web servers.


![step 29 editing inbound rule of the database server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5c8009a5-77ca-4bf4-8163-c8755657bfd0)

set selinux policy:

`sudo setenforce 0`

To make this change permanent – open following config file

`sudo vi /etc/sysconfig/selinux`

![step 26 editing the etc sysconfig selinux file ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d6ab9268-74c9-448d-8d70-41d4b2541e5d)

Updated the website’s configuration to connect to the database in /var/www/html/functions.php.Then Set SELINUX=disabled

`sudo vi /var/www/html/functions.php`

![step 28 editin var www html function php](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c1732318-90bf-4a69-8c67-b0c72b22ba10)
 
then I restarted httpd.

`sudo systemctl restart httpd`

I created a new admin by applying tooling-db.sql script from (tooling-repo directory) to your database using the command:

`mysql -h <databse-private-ip> -u <db-username> -p <database> < tooling-db.sql`

![step 32 connected successfully](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7866de0f-addc-44f9-98c1-3f8cb84db8e3)

checked for the created admin in the database, by running these commands;

`sudo mysql -p`

`mysql> use tooling;`

`mysql> show tables;`

`mysql> select from * users;`

![step 33 apconfiguring the mysql](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a8e0a7ec-9053-476b-a886-9df75f319082)
![step 35 created new user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e7956e4e-4d91-41d7-9e5f-0c663c09e5f4)

Opened the websites in my browser

`http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php`

![webservr 1 public ip](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9fbd8ee2-74b1-4100-89ee-47ded880e49d)
![webserver 1 check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/442344ee-0bdd-4798-aef7-fcf8c4a7de9a)

![webservr 2 public ip](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/012d09fe-1b06-4777-bffa-0b401c3f5d90)
![webserver 2 check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/90267c0a-b7be-4160-90de-ab2361ecf2bf)

![webservr 3 public ip](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2300e739-1976-438b-a47d-6f751e9b14ff)
![webserver 3 check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3546f1bd-faf0-469c-9540-38be52da5310)
#### Implementation done
