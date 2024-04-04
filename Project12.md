# Ansible Refactoring, Assignments & Imports
![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/941d11cd-c0e5-45c7-97cb-df60cf5ff3cf)
In this project, I will continue working with ansible-config-mgt repository and make some improvements to my code. We will learn to refactor our Ansible code, create assignments and learn how to use imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook, allowing for reuse when needed.

## Code Refactoring
Refactoring is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper commenting without affecting the logic. For this project we will move things around a little in our ansible code from the last project but the overall state of the infrastructure will remain the same.

Let us review and improve on our Ansible code!

### Step 1: JD
First, I  made some changes to my Jenkins job. From my  Ansible-Automate-Project, every new change in the codes created a separate directory which is not very convenient when we want to run some commands from one place.

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

Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

![step 5 configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/951ddb7a-9d8a-43f7-b35b-b229dbbb66c6)

![step 5b configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/730f3d2b-b672-4e9c-9048-7a086b119cb5)

The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-artifact` directory. To achieve this, created a build step and choose copy artifacts from other project, specify `ansible` as source project and `/home/ubuntu/ansible-config-artifact` as target directory.

![step 5c configuration of save artifact](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7d3e6d60-241b-4e5c-b4c7-53113dff106b)

![step 6 successful build of the save artifacts](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d798168c-9491-4ce0-8a62-c20a56439a53)

Test your setup by making some change in README.md file inside your ansible-config-mgt repository (right inside main branch).

If both Jenkins jobs have completed one after another, we shall see our files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your main branch.

![step 8 successful build  in jenkins  after editing the readme file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ed0be826-20f2-44ca-a5d0-966331839fd3)

![step 7 files of the ansible-config-mgt in artifacts after the successful build](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7f153532-d1c3-49fc-a137-3b496fe898ec)

## Refactor Ansible code by importing other playbooks into the site.yml file

Before starting to refactor the codes, I ensured that I have pulled down the latest code from the main branch, and created a new branch, named it  `refactor` .

`git checkout -b refactor`


DevOps philosophy implies constant iterative improvement for better efficiency - refactoring is one of the techniques that can be used, but you always have an answer to question "why?".

Why do we need to change something if it works well? good question yea?

In Project 11, all tasks were written in a single playbook `common. yml`, now it is a pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements.

In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families.

Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users, learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

We will now see re-use in action by importing other playbooks.

Within playbooks folder,  I created a new file and named it `site.yml` - This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In otherwords, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that we created previously.


