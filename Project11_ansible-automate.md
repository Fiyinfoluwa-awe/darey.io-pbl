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

The default password was retrieved with the command `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![step 10 getting the admin password](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/67b560e8-ad9d-4bba-9bda-8b1c65e92206)

Installed the suggested plugins and that completes the installation of Jenkins.

![step 11 installed suggested pluginns](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2947b7c4-29b1-4b0d-b50e-0fc94db0385d)

![installing dependencies ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6242fd32-6111-4a9c-ac7a-d500a3607800)

Once plugins installation is done – created an admin user and you will get your Jenkins server address.

![creating first  addmin user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0f4f5a95-5e29-491d-8c1a-3db6cb644246)

![instance configuration](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/922af071-f6b1-4730-9e7f-99ad36d33825)

### CONFIGURE JENKINS TO RETRIEVE SOURCE CODE FROM GITHUB USING WEBHOOKS
In my GitHub account, created a new repository and named it 'ansible-config-mgt'.To enable webhooks in GitHub repository, I opened the `ansible-config-mgt` repository. Then click on "settings" and click on "webhooks".

 I will be configuring a Jenkins job/project. This job will be triggered by GitHub webhooks, which I created, and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on the Jenkins server.

![creating the webhook](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/edcdc1b8-1095-4afa-abe5-e4ab67029aed)


Configured Jenkins build job to archive your repository content every time you make a change to it.

Created a new Freestyle project `ansible` in Jenkins and pointed it to my `ansible-config-mgt` repository.

![connecting github repo to the jenkins playbook](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/af44d775-a98a-4430-9c8a-7f308870b2a1)

![clicked this to fix the error](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9e2def2e-99e9-43f2-8f21-38b848616f11)

edited from 'master' to 'main'

![edited from master to main](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9bf5ffcf-d980-47d2-ba37-18641b3e26f5)

Configured a post-build job to save all (**) files.

![Configure a post-build job to save all () files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/63451084-7d69-4996-b165-2bc83b109663)

![successfully built ansible](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5f96e6d0-d1fa-41b2-9f8c-fbcff3cfe0cf)

Tested my setup by making some change in README.md file in master branch, to be sure that builds starts automatically and Jenkins saves the files (build artifacts) in the folder ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/

![testing by editing the readme file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9c09440d-7ce7-4de0-85ae-7c7658137575)

![checking the test on readme file on terminal](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bd14cecc-0e0f-4d91-b1f4-9181d64a34a9)

###### Note: Trigger Jenkins project execution for main (or master) branch

Tip: Every time you stop/start your Jenkins-Ansible server, you have to reconfigure GitHub webhook to a new IP address. In order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server. Note that Elastic IP is free only when it is being allocated to an EC2 instance so do not forget to release Elastic IP once you terminate your EC2 instance.

### Step 2: Prepare the development environment using Visual Studio Code
The Dev in DevOpsmeans you will be required to write some codes and should have proper tools to make your coding and debugging comfortable. You need an Integrated Development Environment (IDE). I used using Visual Studio Code (VSC). It is free and versatile and will fully satisfy my development needs.  Configured the visual studio code (VScode) to connect to my newly created GitHub repository; ansible-config-mgt.

![opened my repo on vscode](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e0c4a470-efaa-49c4-9323-457c390e7a39)

Cloned my ansible-config-mgt repo to my Jenkins-Ansible instance
git clone <ansible-config-mgt repo link>

![cloning the repo on jenkins-anible repo](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c3b4d431-fe6c-4168-96dd-da942661a894)

### Step 3: Begin Ansible Development
In the ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature.

git checkout -b new_feature


