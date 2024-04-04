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






