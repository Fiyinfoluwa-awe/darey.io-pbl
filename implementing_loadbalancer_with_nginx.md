# IMPLEMENTING A BASIC LOAD BALANCER WITH NGINX 
Provisioned two EC2 instances running on  ubuntu 22.04 operating system,  then I installed apache webserver in them. I opened port 8000 to allow traffic from anywhere.

![step 1 creating of apache servers](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d8890bd5-1445-485f-8a9e-2d242a04d44f)

Opened port 8000. The webservers will be running on port 8000 while the load balancer runs on port 80. Opened port 8000 to allow traffic from anywhere by adding a rule to the security group of each of the webservers.

![editing inbound rule of apache servers to port 8000](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bf218b38-af29-4c8e-847d-7e6582525bba)

SSH into both instances
Installed Apache on both servers.

![ssh into Apache_server1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/95b3addf-fd02-4ce9-aad7-b030ca57b8fe)

![ssh into Apache_server2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/367cb3b9-f9ac-4d85-be86-5315da6a8630)

Installed Apache on both instances as shown in below screenshots.

`sudo apt install apache2`

![step 2 sudo apt apche 2 into server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/87747f17-1833-46ca-a789-d30dc0dcec7d)

![step 2 b sudo apache 2 into server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/abda3e7e-3ea0-4473-896a-f8fe3f9b51b5)

Verified that apache was running on both instances using 

`sudo systemctl status apache2`

![step 3 sudo systemctl status apache2 into server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3a34280f-73ea-4a98-83f4-40071ed8c27a)

![step 3b sudo systemctl status apache2 into server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6ec72bb0-810d-4ae2-b8d5-a82433cf585e)

Configured Apache to serve a page showing its public IP address.

Configure Apache to serve content on port 8000 

`sudo vi /etc/apache2/ports.conf`

![Step 4b editing port on server 1 to 8000](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/06c4407e-90d6-4f69-b9aa-083cc491540a)

![Step 4 editing port on server 2 to 8000](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0c7c30cd-4ac9-433b-b423-2cd79e01abae)

Opened the file /etc/apache2/sites-available/000-default.conf, and changed the port 80 on the virtualhost to 8000.

`sudo vi /etc/apache2/sites-available/000-default.conf`

![step 5 changing the port 80 to 8000 in etcapache2site-available in server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4ec12565-00cd-4beb-aa65-6c81cc5d82a3)

![step 5b changing the port 80 to 8000 in etcapache2site-available in server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/efaa9818-0fde-46e7-948e-77d6d35cf1b9)

Saved and closed the file by first pressing esc key then the command : `wq!`

Restarted apache to load the new configuration using the command 
`sudo systemctl restart apache2`

![step 6 sudo systemctl restart apache2 in server1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/30eb7f3e-9026-4168-8300-c1f6c0211d64)

![step 6b sudo systemctl restart apache2 in server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bb8acb47-cbcf-478f-a35b-9ef5d34eb630)

Created a new html file 

`sudo vi index.html`

Copied the code below and pasted it in the new html files. Replaced 'YOUR_PUBLIC_IP' placeholders with the respective public IP's of the two apache servers.

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

![step 7 sudo vi index html in server1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5316cc32-8c98-43f7-b992-05772c9ad33b)

![step 7b sudo vi index html in server2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4aed30f1-6f03-4b4e-b53c-31864e79352d)

Changed ownership of the index.html file with the command;

`sudo chown www-data:www-data ./index.html`

![step 8 changing ownership of index html in server1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d4805938-39eb-493d-9ec0-b501e2dc1d7b)

![step 8b changing ownership of index html in server2 and restarting](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a3597e2f-93ab-4421-9c09-90e50be5849c)

Overiding the default html file of the Apache Webserver with the command below;

`sudo cp -f ./index.html /var/www/html/index.html`

Restarted the webservers to load the new configuration using the command below;

`sudo systemctl restart apache2`

Checked the pages on my browser:

![step 9 browser page for server 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/99298b34-ec5d-4838-aa74-dc4e64c30307)
![step 9 bbrowser page for server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f4485b64-0776-4c02-a377-858d3ca233c1)

## Configuring  Nginx as a Load Balancer

Provisioned a new EC2 instance running ubuntu 22.04 and ensured that port 80 is open to accept traffic from anywhere.

![step 10 a creation of Nginx server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ab856263-ebb6-45f0-9635-6e1eb8c52a49)

![step 10 editing nginx server inbound rule ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/989e9936-d543-413b-9096-d614bd295fa1)

SSH into the new Ec2 instance ( nginx server), and installed Nginx on the instance using these commands

```
sudo apt update -y 
sudo apt install nginx -y
```

Verified that Nginx was installed 

`sudo systemctl status nginx`

![step 12 sudo apt update nginx server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eefc329d-d2d9-4daf-a4dc-f7323bbed154)

![step 13 sudo apt install nginx -y on nginx server](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/967b6300-2068-4cfa-a368-55827a1c97a5)

![step 14 sudo systemctl status nginx](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7237eaa3-a310-413e-a081-9b7ee3125220)

Opened Nginx configuration file with the command;

`sudo vi /etc/nginx/conf.d/loadbalancer.conf`

Pasted the below configuration in it to configure Nginx to act like a load balancer.

 ```       
upstream backend_servers {

    # your are to replace the public IP and Port to that of your webservers
    server 3.81.33.174:8000; # public IP and port for apache webserver 1
    server 3.80.251.101:8000; # public IP and port for apache webserver 2

}

server {
    listen 80;
    server_name 54.86.25.184; # provide your load balancers public IP address

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

![sudo vi etc ginxconf](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e2062127-85a9-4867-b0c2-07806797c98d)

##### upstream backend_servers defines a group of backend servers. The server lines inside the upstream block list the addresses and ports of your backend servers. Proxy_pass inside the location block sets up the load balancing, passing the requests to the backend servers. The proxy_set_header lines pass necessary headers to the backend servers to correctly handle the requests.

Tested the configuration with the command;

`sudo nginx -t`

![step 15 sudo nginx -t](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4579700a-8e35-49ec-9352-d722bb5b56bd)

There are no errors as in the screenshot above, then I restarted Nginx to load the new configuration with the command;

`sudo systemctl restart nginx`

Pasted the public IP address of Nginx load balancer on a browser
 In the screenshot below, the load balancer serves Apache-Server-1's and Apache server-2's public IP addresses.

 
![loadbalancer for apache server1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/29c04214-e95d-4019-ba78-f606867ee915)

![finnished loadbalancer for apache server 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e652e656-701c-47e3-add6-8e7546ffa281)


THE END




