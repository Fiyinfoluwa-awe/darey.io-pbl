# Ansible Refactoring, Assignments & Imports
![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/941d11cd-c0e5-45c7-97cb-df60cf5ff3cf)
In this project, I will continue working with ansible-config-mgt repository and make some improvements to my code. We will learn to refactor our Ansible code, create assignments and learn how to use imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook, allowing for reuse when needed.

## Code Refactoring
**Refactoring** is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper commenting without affecting the logic. For this project we will move things around a little in our ansible code from the last project but the overall state of the infrastructure will remain the same.

Let us review and improve on our Ansible code!

### Jenkins Job Enhancement
First, I  made some changes to my Jenkins job. From my  [Ansible-Automate-Project](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/blob/main/Project11_ansible-automate.md), every new change in the codes created a separate directory which is not very convenient when we want to run some commands from one place.

Also, it consumes space on the Jenkins server with each subsequent change. I enhanced it by introducing a new Jenkins project/job. To do this, I  will require a copy Artifact plugin.

Logged into my Jenkins-Ansible server and created a new directory called 'ansible-config-artifact'. This will store all artifacts after each build.

`sudo mkdir /home/ubuntu/ansible-config-artifact`

![step 1 mkdir ansible config artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8de744d0-aa33-4fab-83a5-f5b3d8956368)


Changed permissions to this directory so Jenkins could save files there.

`chmod -R 0777 /home/ubuntu/ansible-config-artifact`


![step 2 chmod ansible config ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5e6c6d36-8bd3-490f-bd4f-d08aa1de6f7c)

Go to Jenkins web console `->` Manage Jenkins `->` Manage Plugins. On the Available tab, search for copy Artifact and install this plugin without restarting Jenkins.

![step 3 copy artifact downloading](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/b290a8ce-dbc6-453d-a887-0c0f44ef2d0b)

![step 4 downloaded copy artifacts](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/fc0a5347-4dff-4358-9f72-d8982125ab2b)

Create a new Freestyle project and name it save_artifacts.

This project will be triggered by completion of your existing ansible project. Configure it accordingly.

**Note:** You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

![step 5 configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/951ddb7a-9d8a-43f7-b35b-b229dbbb66c6)

![step 5b configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/730f3d2b-b672-4e9c-9048-7a086b119cb5)

The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-artifact` directory. To achieve this, created a build step and choose copy artifacts from other project, specify `ansible` as source project and `/home/ubuntu/ansible-config-artifact` as target directory.



![step 5c configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7d3e6d60-241b-4e5c-b4c7-53113dff106b)

![step 6 successful build of the save artifacts](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d798168c-9491-4ce0-8a62-c20a56439a53)

Tested my  setup by making some changes in `README.md` file inside my ansible-config-mgt repository (right inside main branch).

If both Jenkins jobs have been completed one after another, I should see the  files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to my main branch.

![step 8 successful build  in jenkins  after editing the readme file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ed0be826-20f2-44ca-a5d0-966331839fd3)

![step 7 files of the ansible-config-mgt in artifacts after the successful build](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7f153532-d1c3-49fc-a137-3b496fe898ec)

## Refactor Ansible code by importing other playbooks into the site.yml file

Before starting to refactor the codes, I ensured that I have pulled down the latest code from the main branch, and created a new branch, named it  `refactor` .

`git checkout -b refactor`

![step 9 git satus git pull](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/64725b04-76ec-4310-8123-648424c85f22)

![step 10a making a refactor branch](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/61fb00cd-b6f2-431d-aa0f-edd9b7b6575d)

DevOps philosophy implies constant iterative improvement for better efficiency - refactoring is one of the techniques that can be used, but you always have an answer to question "why?".

Why do we need to change something if it works well? good question yea?

In Project 11, all tasks were written in a single playbook `common. yml`, now it is a pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements.

In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families.

Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users, learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and re-use them.

We will now see re-use in action by importing other playbooks.

Within playbooks folder,  I created a new file and named it `site.yml` - _This file will now be considered as an entry point into the entire infrastructure configuration._ Other playbooks will be included here as a reference. In otherwords, `site.yml` will become a parent to all other playbooks that will be developed. Including common.yml that we created previously.

![step 10b make site yml ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/93578191-8bbe-43a4-860d-b886f48fea48)

Created a new folder in root of the repository and named it `static-assignments`. This folder is where all other children playbooks will be stored. This is merely for easy organization of our work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. We will see why the folder's name has a prefix of `static` pretty soon.

![step 10 make branch, make siteyml static -assignment](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/56e39820-5bc5-4d80-a9e4-d8ad3cef5c1b)

Moved `common.yml` file into the newly created `static-assignments` folder.

![step 11 moved common yml to static assignment](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f5e1d036-8b2c-4081-b6ab-361d79810836)

Inside `site.yml` file, imported `common.yml` playbook using the below commands;

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

The above code uses a built-in import_playbook Ansible module.

My folder structure should mimic the below:

```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
```

![step 12 imported playbook into siteyml](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e94690a9-c221-4fe2-9f2d-79bc5394d54c)

Ran ansible-playbook command against the dev environment. Since I would need to apply some tasks to my dev servers and wireshark is already installed, I `can go ahead and create another playbook under static-assignments and name it `common-del.yml`. In this playbook, configure deletion of `wireshark` utility.

```
---
- name: update web, nfs and db servers
hosts: webservers, nfs, db
remote_user: ec2-user
become: yes
become_user: root
tasks:
- name: delete wireshark
    yum:
    name: wireshark
    state: removed

- name: update LB server
hosts: lb
remote_user: ubuntu
become: yes
become_user: root
tasks:
- name: delete wireshark
    apt:
    name: wireshark-qt
    state: absent
    autoremove: yes
    purge: yes
    autoclean: yes
```

![common-del-yml folder](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d2cdeee2-177f-43de-8ed6-a66db4201816)

updated `site.yml` with `common-del.yml` instead of `common.yml` and ran it against the dev servers.

![edited the site yml file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ffcb375a-06b6-4f78-bc19-b8d5220c4201)

Pushed  and committed the codes to github and merged to the main branch

We run the commands 

```
git status
git add .
```

![step 13 git status git add ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/27dd9666-153b-4ca4-b209-322cbf931a5a)

![step 14 git pull git commit](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dd52656b-d861-472b-8f68-8b17925732a0)

 created a pull request after  cpmmitting and pushing
 
![step 16 pull request successfully merged](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8247ed9b-e2bf-4455-988f-f08ce0cf8162)

Before we run we have to configure the ssh-agent as shown in the below screenshot

```
eval ssh-agent -s

ssh-add itoro-web-server.pem

ssh -A user@<Server_Public_IP_Address>

ssh-add -l
```

before running the ansible-playbook commmand

The artifact is saved in the `ansible-config-artifact` directory in the jenkins server.

Then run the playbook;

`ansible-playbook -i ansible-config-artifact/inventory/dev.yml ansible-config-artifact/playbooks/site.yaml`

![step 17 playbook delte wireshrk](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4fd56460-39c2-4f08-b1ad-f1058d5585f0)

![step 17b wireshark delete completed](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/da88feb6-d4b5-4597-8d4b-a1d4621a0ebd)

Checking the servers to be sure wireshark was deleted , which was previously installed 
To check that wireshark is deleted on all the servers, we run the comand

`wireshark --version`

on the various target servers.

![loadbalncer wireshark deletion check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d28fcca2-849e-4dd4-bc6b-4238230362f2)

![nfs wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e0ca4613-4a86-4c8d-b20f-b0d4b80973df)

![webserver1 wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/832aab3e-4ebe-4e77-b3b8-d199633660d0)

![webserver 2 wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/031046f2-c7fb-426c-a8fa-08a500724df5)

![database wireshark check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f4bb11af-fcaf-4295-8c69-6bb4f8b6dd84)

there was all successfully deleted on each server.Now we have have a ready solution to install/delete packages on multiple servers with just one command.

## CONFIGURE UAT WEBSERVERS WITH A ROLE ‘WEBSERVER’

 I configured two new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, I will use a dedicated **role** to make the configuration reusable.

So i launched two EC2 instances using RHEL 8 image, we will use them as our uat servers; Web1-UAT and Web2-UAT.

![uat-webservers created ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/09df4755-3dd7-49ea-8c17-039c28e5256b)

**Tip:** Do not forget to stop EC2 instances that you are not using at the moment to avoid paying extra. For now, you only need 2 new RHEL 8 servers as Web Servers and 1 existing Jenkins-Ansible server up and running.
To create a `role`, you must create a directory called `roles/`, relative to the playbook file or in the `/etc/ansible` directory.

There are two ways we can create this folder structure:

First, we can use an Ansible utility called `ansible-galaxy` inside ansible-config-mgt/roles directory (you need to create roles directory upfront).

```
mkdir roles
cd roles
ansible-galaxy init webserver
```
 secondly, by create the directory/files structure manually.

**Note:** You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

The entire folder structure should look like below, but if you create it manually- you can skip creating tests, files vars or remove them if you used ansible-galaxy;

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```

After removing unnecessary directories and files, the roles structure should look like below;

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```

![created roles (webserver) structure  new ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/49a38dcb-5482-45d3-8d83-f32d7ec5f037)
