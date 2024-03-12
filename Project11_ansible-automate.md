# ANSIBLE - AUTOMATE PROJECT


![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/642940e3-fbd8-4daf-bf03-2d086c834f6b)

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

Performed the  initial Jenkins setup.

From my browser, accessed `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`. I was prompted for a default admin password.

![step 9 jenkins browser check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/506b6516-66e8-4445-a729-3698e10a96db)

The default password was retrieved with the command `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![step 10 getting the admin password](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/67b560e8-ad9d-4bba-9bda-8b1c65e92206)

Installed the suggested plugins and that completes the installation of Jenkins.

![step 11 installed suggested pluginns](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/2947b7c4-29b1-4b0d-b50e-0fc94db0385d)

![installing dependencies ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6242fd32-6111-4a9c-ac7a-d500a3607800)

Once plugins installation is done – created an admin user and I got my Jenkins server address.

![creating first  addmin user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0f4f5a95-5e29-491d-8c1a-3db6cb644246)

![instance configuration](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/922af071-f6b1-4730-9e7f-99ad36d33825)

### CONFIGURE JENKINS TO RETRIEVE SOURCE CODE FROM GITHUB USING WEBHOOKS
In my GitHub account, created a new repository and named it 'ansible-config-mgt'.To enable webhooks in GitHub repository, I opened the `ansible-config-mgt` repository. Then click on "settings" and click on "webhooks".

I will be configuring a Jenkins job/project. This job will be triggered by GitHub webhooks, which I created, and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on the Jenkins server.

![creating the webhook](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/edcdc1b8-1095-4afa-abe5-e4ab67029aed)


Configured Jenkins build job to archive my repository content, every time I make a change to it.

Created a new Freestyle project `ansible` in Jenkins and pointed it to my `ansible-config-mgt` repository.

![connecting github repo to the jenkins playbook](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/af44d775-a98a-4430-9c8a-7f308870b2a1)

![clicked this to fix the error](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9e2def2e-99e9-43f2-8f21-38b848616f11)

edited from 'master' to 'main'

![edited from master to main](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9bf5ffcf-d980-47d2-ba37-18641b3e26f5)

Configured a post-build job to save all (**) files.

![Configure a post-build job to save all () files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/63451084-7d69-4996-b165-2bc83b109663)

![successfully built ansible](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5f96e6d0-d1fa-41b2-9f8c-fbcff3cfe0cf)

Tested my setup by making some change in README.md file in master branch, to be sure that builds starts automatically and Jenkins saves the files (build artifacts) in the folder 

`ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

![testing by editing the readme file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9c09440d-7ce7-4de0-85ae-7c7658137575)

![checking the test on readme file on terminal](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bd14cecc-0e0f-4d91-b1f4-9181d64a34a9)

###### Note: Trigger Jenkins project execution for main (or master) branch

Tip: Every time you stop/start your Jenkins-Ansible server, you have to reconfigure GitHub webhook to a new IP address. In order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server. Note that Elastic IP is free only when it is being allocated to an EC2 instance so do not forget to release Elastic IP once you terminate your EC2 instance.

### Step 2: Prepare the development environment using Visual Studio Code
The Dev in DevOps means you will be required to write some codes and should have proper tools to make your coding and debugging comfortable. You need an Integrated Development Environment (IDE). I used using Visual Studio Code (VSC). It is free and versatile and will fully satisfy my development needs.  Configured the visual studio code (VScode) to connect to my newly created GitHub repository; ansible-config-mgt.

![opened my repo on vscode](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e0c4a470-efaa-49c4-9323-457c390e7a39)

Cloned my ansible-config-mgt repo to my Jenkins-Ansible instance

`git clone <ansible-config-mgt repo link>`

![cloning the repo on jenkins-anible repo](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c3b4d431-fe6c-4168-96dd-da942661a894)

### Step 3: Begin Ansible Development
In the ansible-config-mgt GitHub repository, created a new branch that will be used for development of a new feature.

`git checkout -b new_feature`

Checkout the newly created feature branch to your local machine and start building your code and directory structure.

`git checkout -b new_feature`

Created a directory named playbooks. It will be used to store all my playbook files. 

`mkdir playbooks`

![creating branch, mkdir playbook ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/251e3c34-bab2-460d-b899-2946eb7be83e)

Created a directory named inventory. It will be used to keep my hosts organised. 

`mkdir inventory`

Within the playbooks folder, created my first playbook and named it 'common.yml'

```
cd playbooks
 touch common.yml
```

![creating inevntory , common yml ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/63756f6f-adf4-4db1-8ecd-5638033e4f30)

Within the inventory folder, created an inventory file for each environment (Development, Staging, Testing and Production) named dev, staging, uat and prod respectively. These files use .ini languages style to configure Ansible hosts.

![creating the dev staging uat prod files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/19a9d5a3-40f2-4451-934b-2196fc6e30f4)

![successfully created the dev staging uat prod files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/10bc65ee-9cf7-4775-838f-547ec5662b07)

### Step 4: Set up an Ansible Inventory
An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since my intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize my hosts in such an Inventory.

Saved the below inventory structure in the inventory/dev file to start configuring my development servers. Ensured to replace the IP addresses according to my own setup.

##### Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host. For this we can implement the concept of ssh-agent. Now I need to import key into ssh-agent, uding these commands;

```
eval `ssh-agent -s`
ssh-add -k <path-to-private-key>
```

Confirmed the key has been added with the command 

`ssh-add -l`

![adding the key pair , importin it ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/979d4ec4-b371-434c-82c6-fec039ed4e20)

SSH into the Jenkins-Ansible server using ssh-agent 

`ssh -A ubuntu@public-ip`

![ssh connect to my instance thru vscode after editing d config file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b0aa8385-d7bf-4926-b93f-c0daaf488c99)

Updated the inventory/dev.yml file with the below code snippet.

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user=ec2-user

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user=ec2-user
<Web-Server2-Private-IP-Address> ansible_ssh_user=ec2-user

[db]
<Database-Private-IP-Address> ansible_ssh_user=ec2-user 

#[lb]
#<Load-Balancer-Private-IP-Address> ansible_ssh_user=ubuntu

```
updated the code with the IP addresses and usernames of the respective servers to be managed

edited the username of my db server from ec2-user to ubuntu , since its running on ubuntu.

![updating the dev yml file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/61b2d62f-2705-4997-a455-8b9911286618)

![error ith puting two same server , will correct that now](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/44f523aa-455c-4c72-9c80-74364db8dc3d)


### Step 5: Create a common playbook
It is time to begin giving Ansible the instructions on what I need to be performed on all servers listed in inventory/dev.

In common.yml playbook,  I wrote the configuration for repeatable, re-usable and multi-machine tasks that is common to systems within the infrastructure.

Updated the playbooks/common.yml file with the below code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  become: yes
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest


- name: update LB server
  hosts: lb
  become: yes
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

```

The above playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.
 
I edited the code abit to suit my database server which is ubuntu 
  
![first edit on common yml , didnt even work](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ad89f58b-3f06-410e-9801-513126d83ba6)


##### Feel free to update this playbook with the following tasks:

* Create a directory and a file inside it
* Change timezone on all servers
* Run some shell script


### Step 6: Update Git with the latest code
Now all of the directories and files live on my local machine and I need to push changes made locally to GitHub. In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with the help of Git.

In many organizations, there is a development rule that does not allow the deployment of any code until it has been reviewed by an extra pair of eyes - It is called the Four-Eyes Principle.

Now I have a separate branch,  I will raise a Pull Request (PR), get my branch peer reviewed and merged to the master branch.

Commit my  code into GitHub: Using git commands to add, commit and push your branch to GitHub.

```
git status

git add <selected files>

git commit -m "commit message"

git push --set-upstream origin new_feature
```

![committed the inventory files](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3a2eb2de-2ed4-4dad-842d-2637494d10ed)

![commited playbook files ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7cf63ad7-7a36-422c-8726-9bbadee55b01)

Created a Pull Request (PR)

![pull request](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c41c43ac-8058-4618-a839-f86820fa3dc8)

![creating the pull request](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/703eae40-e6be-44f6-9117-74845e13dfa3)

Wore the hat of another developer for a second, and acted as a reviewer.

![creating the pull request](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/29d01a41-4ce3-47e7-b0ce-69dfb570d6e7)

If the reviewer is happy with my  new feature development,  I would go ahead to merge the code to the master branch, which I did
![merged the commit on github ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/431f1d7a-c96c-4d85-b523-e083bda307ba)

Headed back to my terminal,  to checkout from the feature branch into the master, and pull down the latest changes.

`git checkout main`

![git checkout to main ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dfd73db3-6e83-4a75-9a19-c766a1c562b3)

Once my code changes in master branch, Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![checking our merge on jenkins](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/fc8cb8f1-40c8-4c84-8299-2009930d9a9e)

![checking our merge or branch on the terminal](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/adcacdef-ed14-4846-807c-d579680db8cf)

### Step 7: Run first Ansible test
Now it is time to execute ansible-playbook command and verify that the playbook actually works.

 First, I setup VSCode to connect to my instance.

![connected my instance on vs-code and opened my ubuntu file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8b0ac1b0-1e73-499f-a7c6-26295c595cdc)

Ran the playbook using the command

```
# change to your ansible directory
cd ansible-config-mgt

# run the playbook
ansible-playbook -i inventory/dev.yml playbooks/common.yml

# actual command
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/8/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/8/archive/playbooks/common.yml
```

From the screenshots below, the playbook ran successfully. Successful connections were made to all servers and changes (installing wireshark) were made. I confirmed by logging into each server.

![Playbook finally worked](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/48e878b1-a293-4ff5-af58-5932a273bc2c)


![wb 1 wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a88f4f94-62ce-43bb-8021-96b3b7ff2ae1)

![wireshark check on webserver 2 on gitbash](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1002b8ae-aaee-406f-9bcb-bff9097a74d7)

![Databse server wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/674eee15-f16d-4133-9cbe-40674cd48ab9)

![loadbalancer check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0a1bbaa1-33d2-44ec-a6c4-f4870df62e5e)



