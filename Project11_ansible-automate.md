# ANSIBLE - AUTOMATE PROJECT

ANSIBLE is a software tool that provides simple but powerful automation for cross-platform computer support.

It is primarily intended for IT professionals, who use it for application deployment, updates on workstations and servers, cloud provisioning, configuration management, intra-service orchestration, and nearly anything a systems administrator does on a weekly or daily basis. Ansible doesn't depend on agent software and has no additional security infrastructure, so it's easy to deploy.

While Ansible may be at the forefront of automation, systems administration, and DevOps, it's also useful to everyday users. Ansible allows you to configure not just one computer, but potentially a whole network of computers at once, and using it requires no programming skills. Instructions written for Ansible are human- readable. Whether you're entirely new to computers or an expert, Ansible files are easy to understand.

Ansible Client as a Jump Server (Bastion Host)
A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server - it provides better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets - Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

bastion

In previous projects we had to perform lots of manual operations to set up virtual servers, install and configure required software and deploy our web applications especially on projects 7 through 10. This project however, will make us appreciate DevOps tools more by making the most of the routine tasks automated with Ansible Configuration Management, at the same time building confidence in writing code using declarative languages such as YAML.

In this project, I will install and configure Ansible client as a Jump Server/Bastion Host and create a simple Ansible Playbook to automate servers configuration.

## Install and Configure Ansible on EC2 Instance
### Installing and Configuring Jenkins and Ansible on EC2

I created an AWS EC2 Server Instance running on  Ubuntu OS {Jenkins-Ansible}.

![step 1  created jenkins-ansible instance](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9583a33c-35df-4c02-a932-162488fd7926)

Installed JDK (Since Jenkins is a Java-based application)


```
sudo apt update  
sudo apt install openjdk-11-jre

#Confirm Java installation  
java -version
```


![step 2 sudo apt update](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e757e26c-9a02-41ca-875e-df1080c66972)

![step 5 install jenkins](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/86cd4ade-ce20-4edb-8c54-a3de7dbca4a1)

![step 6 java version](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1553f8da-96cd-41f8-8b8f-d5aec60f1c6d)

Installed Jenkins. You can refer to the official Jenkins Documentation

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update

sudo apt-get install -y jenkins

#Confirm Jenkins is up and running

sudo systemctl status jenkins
```

![step 7 systemctl status jenkins](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2f71048c-83b1-4628-9335-c051eab0cc56)

By default, Jenkins server uses TCP port 8080. So opened this port, by creating a new Inbound Rule in the EC2 Security Group.

![step 8 editing the inbound rule of jenkins-ansible](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eb2b17a9-5803-4fd2-bd6a-03ae0b3301b9)

Performed  initial Jenkins setup.

From my browser, accessed `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`. I was prompted for a default admin password.

![step 9 jenkins browser check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/506b6516-66e8-4445-a729-3698e10a96db)



