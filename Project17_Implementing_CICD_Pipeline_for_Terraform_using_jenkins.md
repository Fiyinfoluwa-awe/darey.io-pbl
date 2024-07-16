# IMPLEMENTING CICD PIPELINE FOR TERRAFORM USING JENKINS

I will be implementing  a seamless CI/CD pipeline for Terraform using Jenkins int this project, enabling automated testing, deployment, and management of infrastructure-as-code with ease.

## Introduction to CI/CD and its importance in software development

In today's rapidly evolving IT landscape, efficient and reliable deployment of infrastructure is paramount. Continuous Integration and Continuous Deployment (CI/CD) have emerged as indispensable practices, fostering automation and agility in the software development lifecycle. In this project, we will explore the powerful combination of Terraform, a leading Infrastructure as Code (IaC) tool, and Jenkins, a widely used automation server, to streamline and enhance infrastructure deployment workflows.

### Project Overview
---

In the journey ahead, we will delve into the intricacies of building a robust CI/CD pipeline specifically tailored for Terraform projects. By automating the building, testing, and deployment of infrastructure changes, these pipelines enhance speed, reliability, and consistency across environments. The use of Infrastructure as Code (laC) with Terraform ensures reproducibility and scalability, while Jenkins facilitates collaborative development, visibility, and continuous integration and deployment as you will see in this project. This approach not only reduces time-to-market but also promotes resource efficiency, cost optimization, and compliance with security standards. Overall, CI/CD pipelines with Terraform and Jenkins empower organizations to adapt quickly to changing business requirements, fostering a culture of automation and continuous improvement in the dynamic landscape of modern software development and operations.

### Setting up the Enviroment 
---

I will start by setting up a Jenkins server running in a docker container. Then will create a Dockerfile to define the configuration for our Jenkins server. This Dockerfile will include the necessary dependencies and configurations to run Jenkins seamlessly, and also to run terraform cli.

*Dockerfile for Jenkins*

***Note:** Jenkins comes with a docker image that can be used out of the box to run a container with all the relevant dependencies for Jenkins. But because we have unique requirements to run terraform, we need to find a way to extend the readily available jenkins image.*

1) Created  an EC2 instance , make sure that it is a `t2.medium` or run Docker Desktop. In my cae, I used an EC2 instance.

![signing into the instance](https://github.com/user-attachments/assets/a44204bb-49ce-4469-8d02-4b3df84f347a)

2) Next, I need to have Docker running in my instance or Dockerdesktop. To install Docker in my instance , I ran

`sudo apt-get install -y docker -ce`

![step 2 sudo apt install docker -ce](https://github.com/user-attachments/assets/bb3d4d45-3788-4063-af69-ddf13224fec2)

3) Now that Docker is installed, verify that it is installed properly with `sudo systemctl status docker`

![step 3 sudo systemctl status docker](https://github.com/user-attachments/assets/c4af2fbf-3412-40dd-ba1a-9b7862177f24)

**Now creating the Dockerfile 
4) Create a directory and name it `terraform-with-cicd` and `cd` into the directory.
5) Inside the directory , create a file and name it `Dockerfile`
6) Run `sudo vi Dockerfile` and add this script 

```
 # Use the official Jenkins base image
 FROM jenkins/jenkins:lts

 # Switch to the root user to install additional packages
 USER root

 # Install necessary tools and dependencies (e.g., Git, unzip, wget, software-properties-common)
 RUN apt-get update && apt-get install -y \
     git \
     unzip \
     wget \
     software-properties-common \
     && rm -rf /var/lib/apt/lists/*

 # Install Terraform
 RUN apt-get update && apt-get install -y gnupg software-properties-common wget \
     && wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg \
     && gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint \
     && echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list \
     && apt-get update && apt-get install -y terraform \
     && rm -rf /var/lib/apt/lists/*

 # Set the working directory
 WORKDIR /app

 # Print Terraform version to verify installation
 RUN terraform --version

 # Switch back to the Jenkins user
 USER jenkins

```

![step 4 mkdir terraform   touch dockerfile ](https://github.com/user-attachments/assets/55d70ddf-025b-4e57-85db-a8aa4232ff95)

![cat dockerfile](https://github.com/user-attachments/assets/6acb8666-e98d-49ea-9a86-22c164ef047f)

## EXPALINING THE DOCKERFILE 
- **FROM jenkins/jenkins:lts:** This line specifies the base image for my dockerfile, this image will be pulled from the Docker Hub since I do not have it locally.
- **USER root:** this command switched to the root user within the Docker image. this is done to perform actions as a root user, for permissions such as installing packages.
-  The below script installs neccessary tools and dependencies. The `apt-get update` command refreshes the package list and `apt-get install` installs the packages `( git, unzip, wget, software-properties-common). The `&&` is used to chain commands and `rm -rf /var/lib/apt/lists/*` removes unneccessary packages lists, helping to reduce the size of the Docker image.

```
  RUN apt-get update && apt-get install -y \
    git \
    unzip \
    wget \
    software-properties-common \
    && rm -rf /var/lib/apt/lists/*
```

- **Install Terraform**

```
  RUN apt-get update && apt-get install -y gnupg software-properties-common wget \
    && wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    && gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint \
    && echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update && apt-get install -y terraform \
    && rm -rf /var/lib/apt/lists/*
```
The above block installs Terraform. It follows similar steps as before: updating the package list, installing dependencies, adding HashiCorp's GPG key, configuring the repository, updating again and finally installing Terraform. Again, it includes cleaning up unnecessary package lists.

A quick note on GPG Key: GPG (GNU Privacy Guard) is a free and open-source software for encryptingand signing data. In the context of software distribution, GPG keys are used to verify the integrity and authenticity of packages.

If you really want to get into the details of what the block of code is doing you can follow below:

`wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg`

1) *Downloading HashiCorp's GPG Key:*
  
   `wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg`

  * This command downloads HashiCorp's GPG key from `https://apt.releases.hashicorp.com/gpg`

  * `gpg --dearmor` converts the key into a format that can be easily used by tools.

  * `tee /usr/share/keyrings/hashicorp-archive-keyring.gpg` writes the key to the specified file with `.gpg` extension.

2) *Displaying the GPG key fingerprint:*

`gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint`

  * This command displays the fingerprint of the GPG key. The fingerprint is a unique identifier for the key and is used to verify its authenticity.

3) *Confuguring the repository with the GPG key:*

   `echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list`

    * This command adds HashiCorp's repository configuration to the system's package manager (`apt` in this case).
    * It specifies the GPG key to be used for package verification.

4) *Updating and installing Terraform:*
   `RUN apt-get update && apt-get install -y terraform && rm -rf /var/lib/apt/lists/*`

   * These commands update the package list, install Terraform, and then remove unnecessary packages lists to reduce the size of the Docker image

5) `WORKDIR /app:` This line sets the working directory to /app. this is where you will enter the container.
6) `RUN terraform --version:` this command prints the version of Terraform to the console , allowing you to verify that the installation was successful.
7) `USER jenkins:` this command switches the jenkins user returning to a lower privelege level. This is good for security practice to minimize the risk of running proccesses as the root user within the container.

  ## Building and Running the Docker Image

  Make sure that you are inside the folder containing the `Dockerfile`.This is generally referred to as the `Docker Build Context`. The build context is the set of files located in the specified directory or path when you build a Docker image using the `docker build command`.  The content of the build is sent to the Docker daemon during the build process, and it serves as the source for building the Docker image.

  1. Build the custom jenkins image

     ` docker build -t jenkins-server .`

     *Notice the `.` at the end. That is the `docker build context`, meaning the current directory where the Dockerfile is.

     ![step 5b finsihed docker build](https://github.com/user-attachments/assets/b97510b7-daf3-485c-896e-74f5fd4f7be7)


   2. Run the image into a docker container.

      `docker run -d -p 8080:8080 --name jenkins-server jenkins-server`

      the output should be a hashdata like this:

      `800f8f48466b3419d7cbf37908b12f146120b31260147bdd3b433d23e60f976b`
      
      ![step 6 docker run 8080](https://github.com/user-attachments/assets/2106db97-08cf-4b46-8b86-76025b5d423a)

      **Let's break down the command for clear understanding:**

      * `docker run:` This is the command used to run a Docker container.

      * `-d`: This flag stands for "detach". It runs the container in the background, allowing you to continue using the terminal for other commands.

      * `-p 8080:8080`: This flag maps the port 8080 from the host to the port 8080 inside the container. It means that you can access the application running 
        inside the container on your host machine's port 8080. Simply go to the browser and put in there localhost:8080 to display the application.

      * `--name jenkins-server`: This flag assigns a name to the container. In this case, the name is set to jenkins-server.

       * `jenkins-server`: This is the name of the Docker image that we want to run. It refers to the image named jenkins-server that was built earlier when we 
           ran `docker build` command.


   3. Check the container is running, using; `docker ps`. The output should be similar to;

  ` CONTAINER ID   IMAGE            COMMAND                  CREATED              STATUS              PORTS                               NAMES
 800f8f48466b   jenkins-server   "/usr/bin/tini -- /u…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp, 50000/tcp   jenkins-server`
     
 ![step 6 docker run 8080](https://github.com/user-attachments/assets/2c21f253-678a-460e-b7da-a551cbb01baa)

 4. Access the jenkins server from the web browser on `localhost:8080`, if you are running Docker Desktop. But since I am running an EC2 instance, so I will use  `publicIP:8080`.

    *Note: Remember to open port 8080 in the security group if you are implementing using an EC2 instance.*

    ![step 7 jenkins sign in](https://github.com/user-attachments/assets/4e5ee084-b07a-4ab4-b007-c69ea1f6dfb5)

  * Run `var/jenkins_home/secrets/initialAdminPassword` to get the Jenkins password, enter it and follow the flow of the website, enter all required information.
  * Create first admin user and access Jenkins


5. Access the Jenkins server directly inside the container, using;

   `docker exec -it  <containerID>  /bin/bash`

**Let's breakdown the command:**

* `docker exec`: This command is used to execute a command in a running Docker container.
* `it`: These flags are often used together.
    * `i` stands for 'interactive', which allows you to interact with the container.
    * `t` allocates a pseudo-TTY, or terminal, enabling a more interactive session.
 * `containerID` stands for the container ID or container name. It uniquely identifies the running container, so replace it with the actual ID or name of the your container. You can get this from the output of `docker ps`
 * `/bin/bash`: This is the command that you want to execute inside the container. In this example, it's launching an interactive Bash shell (bash) within the specified container.

So, when you run this command, Docker will:

  * Use the `exec` command to execute a command in a running container.
  * Use the `it` flags to make the intercation with the container interactive.
  * Specify the Container ID or Container Name
  * Specify the command to execute inside the container, which is `/bin/bash`.

You will also notice that once you get into the container, the directory you will find yourself is the `/app`. Which is from the `WORKDIR` directive inside the `Dockerfile`

The output of `docker exec -it  <containerID>  /bin/bash` should look like this;

`jenkins@<containerID>:/app$ pwd
/app`

6. Retrive the initial Jenkins admin password

From the web UI, there is an instruction to retrive the initial admin password from

`/var/jenkins_home/secrets/initialAdminPassword`

Inside the container, run:
the output should look like this;

`jenkins@800f8f48466b:/app$ cat /var/jenkins_home/secrets/initialAdminPassword
a845868b3a404f39b48b1b05137b4888`

7. Installing Jenkins plugins
   
   * Paste the initial passwird in the web
   * Click on "install suggested plugins"
  
 8. Create first admin user and access Jenkins


![step 8 Jenkins setup](https://github.com/user-attachments/assets/320eaa43-6995-4bdc-b57a-6d1147c97af5)



## Setting up Jenkins For Jenkins for Terraform CI/CD
---

**Jenkins Pipelines for Terraform**

The first question that may come to your mind is *"why do we need to have CI/CD for our infrastructure?"*, 8"I thought CI/CD is for the software code that developers write?"* - Well, you are correct if you have thought of those questions. But the infrastructure in which the software is being operated can also be treated in similar manner, especially now that we can have the entire infrastructure as code through `Terraform`.

Treating our infrastructure the same way we would treat our software presents a set of benefits such as:

1. Automating the release of features into our cloud infrastructure
2. Experiencing a fast feedback loop for infrastructure changes
3. Continous testing of our infrastructure
4. Collaboration and Team Productivity
5. Rapid Release Cycle and
6. Risk Reduction in production

Setup Git Respository with Terraform code
---

The use case we will satisfy is that;

  1. The terraform code has an existing set of resources that it creates in your preferred cloud provider.
  2. You as a DevOps engineer intend to create an additional resource by updating the code base with the new resource that needs to be created

Therefore, the first logical thing to have is an existing terraform code. If you dont have your own code, you can simply use this github link and fork the respository into your own github account https://github.com/dareyio/terraform-aws-pipeline.git ; It creates Networking later , also - It provisions kubernetes cluster using EKS

![step 9 forked aws pipeline darey ](https://github.com/user-attachments/assets/ea833a62-837d-4d2d-bc4f-982296aef36e)


Then do the following to test that the code can create existing resources; 

   1. The `provider.tf` file has an s3 backend configuration. You will need to create your own bucket and update the code
        * Create an S3 bucket in your AWS account

         ![step 10 s3 busket](https://github.com/user-attachments/assets/63d1fb04-7a5a-4845-b112-626242bc1e8f)

        * Update the bucket name and region from the backend configuration

          ![step 11 edited provider tf](https://github.com/user-attachments/assets/17782ccb-e2a1-4489-9410-feda5b6cfa49)

     
   2. Push your lastest changes to Github

      ![step 12 git add provider tf](https://github.com/user-attachments/assets/a4894931-5fe0-47e9-9c45-15248e287fe1)

   3. Run Terraform init, plan and apply , to confirm everything works fine.

      ![step 13 terraform init](https://github.com/user-attachments/assets/a4faef2d-7d1e-41d1-bd07-56a862451a43)

      ![step 13 c terraform init complete](https://github.com/user-attachments/assets/7ac9e7ef-6fa4-432b-aaac-f7c8eb8e15ca)

      *terraform plan*

      ![step 14 terraform plan](https://github.com/user-attachments/assets/9371f98b-1769-455f-b52e-49dcf8750a32)

      *terraform apply*

      ![step 15 terraform plan](https://github.com/user-attachments/assets/770db94f-9447-4a86-b4b9-e4c3b8ef6067)


      ![the new object in the s3 after terraform paln](https://github.com/user-attachments/assets/077fdce4-5c26-4670-86fd-34592bf7a577)


**Connect the Github repository to Jenkins**

  1. Install Jenkins Github Plugin;

      * Open Jenkins in your web browser `<publicIP:8080>`
      * Navigate to `Manage Jenkins` --> `Plugins`
    
        ![step 16 manage jenkins](https://github.com/user-attachments/assets/a918635b-dbca-415e-a199-62a2f259333e)

      * Click on `Available plugins`
      * Scroll down and select the `Github Integration` plugin for installation
    
        ![step 17 github integration jenkins plugin](https://github.com/user-attachments/assets/82803997-576f-4d98-bcc9-e37e58effc56)

        ![download of github integration](https://github.com/user-attachments/assets/cb4e6680-538f-4f4f-a01d-83d45b45790a)

   2. Install the "Terraform" plugin, ths plugin enables seamless integration of Terraform into the terraform pipleines

      The Terraform plugin for Jenkins enables seamless integration of Terraform into Jenkins pipelines. With this plugin, you can manage the installation of mutliple terraform versions, update terraform modules and do much more as documented in the official site https://plugins.jenkins.io/terraform/
      
      Navigate to *Manage Jenkins --> Plugins --> Available Plugins --> Terraform

      ![step 18 terraform jenkins plugin](https://github.com/user-attachments/assets/24bbee8e-ac27-4f39-b91c-4b33cf3e78df)

  3. Install the "**AWS Credentials**" for securely managing and using AWS credentials within Jenkins.

     The AWS Credentials plugin is essential for securely managing and utilizing AWS (Amazon Web Services) credentials within Jenkins. This plugin facilitates the secure storage and retrieval of AWS access and secret keys, ensuring that Jenkins jobs and pipelines can securely interact with AWS services during the execution of various tasks and deployments, including terraform runs. You can read more about the plugin here https://plugins.jenkins.io/aws-credentials/

     Navigate to *Manage Jenkins --> Plugins --> Available plugins --> AWS Credentials

     ![step 19 aws credential jenkins plugin](https://github.com/user-attachments/assets/c9aa157a-7547-48f5-8704-d75adfa651de)

     ![step 19b aws credential jenkins plugin](https://github.com/user-attachments/assets/b0f06714-e3e8-4a5c-8554-5fbee3988446)

     * If everything is successful, then click to restart it, run with it;

       `docker restart <containerID>`

     * Check the status of the container again and it should be back up.
     * Then go back to the browser and access Jenkins.
     
    
       ![step 20 docker restart ](https://github.com/user-attachments/assets/d1e0a486-44b1-4ba8-87f0-b19be4d595d3)
    
       ![step 20 restart jenkins](https://github.com/user-attachments/assets/d1750fe6-4baf-40fb-96c2-0c2212a5ef87)

     * Back in Jenkins UI, navigate to the **"Installed plugins"** section and you should now see the newly installed plugin and it is enabled.
    
       ![step 21b checking the installed jenkins plugins](https://github.com/user-attachments/assets/7d83ee54-f4ff-4140-ba82-0964f82ba583)
    
       ![step 21 checking the installed jenkins plugins](https://github.com/user-attachments/assets/a0ec9625-08d6-4a76-bccd-28ae84c8b69e)

       ![step 21c checking the installed jenkins plugins](https://github.com/user-attachments/assets/ab139951-3543-45b7-be20-34067c6ce45a)


     

 ## Configure Github Credentials in Jenkins

Jenkins needs to know how to connect to Github, otherwise i real world cases where repositories are private, it won't know how to access the repository. Hence, the first thing we need to do is to store the github credentials in Jenkins.

*In Github, navigate to your profile --> Click on "Setting" --> then scroll down to --> "Developer Settings"*

*Generate an access token*

![step 22 generate access token](https://github.com/user-attachments/assets/cdd67507-758d-4320-9988-747cf8f463b0)

![step 22b generate access token](https://github.com/user-attachments/assets/d19c0401-d48b-4afe-90ae-d138ba83de18)

![step 22b generate access token](https://github.com/user-attachments/assets/6b890ef8-3e84-45b7-8d4e-144399114f20)

*Copy the access token and save in a notepad for use later*

![step 22d access token generated ](https://github.com/user-attachments/assets/a42fced8-dddc-4656-baf2-d327611f3816)

*In the Jenkins navigate to "Manage Jenkins" --> Click on "Credentials"

![step 23 credentials](https://github.com/user-attachments/assets/5c3176f4-b809-4fdd-ac10-3c86860121be)

*Click om the arrow next to "global" and select "add credentials"*

![step 23b add credentials](https://github.com/user-attachments/assets/9045176b-4450-460a-a3cb-37525f71749a)

*Select username and password.Use the "access token generated earlier as your password, and specify anything descriptive as your ID*

![step 23c credentials created](https://github.com/user-attachments/assets/31d38e8d-ff72-48a5-ab61-20fc39cea488)

*In the credentials section, you will be able to see the created credential*

![step 23d credentials created](https://github.com/user-attachments/assets/070710cd-8754-4cf0-981b-19863febb5d2)

![step 23e credentials created](https://github.com/user-attachments/assets/af167854-6c76-491f-889e-5327b75f6cec)

* Create a second credential for AWS secret and access key. If you have installed the AWS credentials plugin, you will see the "AWS Credentials" kind as shown below. Simply add the new AWS secret and access key generated from AWS console (from creating an IAM user).

![step 24 creating aws credntials ](https://github.com/user-attachments/assets/6f1ce563-029b-4de8-8cb4-fb80c5b6b7c8)

![step 24b created aws credntials ](https://github.com/user-attachments/assets/b6b1cd80-c724-4327-9d76-7000ee7f0b95)


##  Set up a Jenkins Multibranch Pipeline;

   * From the Jenkins dashboard, click on "New Item"

     ![step 25 creating new item terraform cicd](https://github.com/user-attachments/assets/ffa2a349-5d86-45bc-a76e-8461dd7f1aff)

   * Give it a name and description

     ![step 25b new item terraform cicd config](https://github.com/user-attachments/assets/c75d9fb5-b4f7-4bef-9898-821eb3086fc1)

   * Select the type of source of the code and Jenkinsfile

     ![step 25c new item terraform cicd config](https://github.com/user-attachments/assets/358b3f82-7528-422c-8b17-87ccc33afafb)

   * Select the credentials to be used to connect to Github from Jenkins and add the repository URL that was forked earlier. Leave everything as default and save it. 

     ![step 25d new item terraform cicd config png github config](https://github.com/user-attachments/assets/3f9396b4-32c6-4e73-80f5-543574042ce9)

     ![step 25e new item terraform cicd config](https://github.com/user-attachments/assets/d31007ec-385d-4449-a7d5-c6357a462110)

     Pipeline run and console, *Terraform apply* failed

     ![terraform build failed](https://github.com/user-attachments/assets/6d0b3344-89c4-4fb1-a718-bf7272728810)

     the output

     ![build 4 administrator approve or reject build error](https://github.com/user-attachments/assets/5d078894-5653-465d-8972-d4437ced7bd1)

     to fix it the scripts not permitted to use method, we will go to the Jenkins Dashbord, click on *Manage Jenkins*, scroll down to the *Security Tab* and click on *In-process Script Approval*

     ![in process script approval](https://github.com/user-attachments/assets/ab07a2d6-6483-4ca3-9020-424cbd3561e8)

     Signed approval

     ![signed approval](https://github.com/user-attachments/assets/5d91e621-72bb-4be7-bc7c-4bcc42e5cab5)

     A manual intervention step asking for confirmation before proceeding. If 'yes' is clicked, it runs the `terraform init && terraform apply`

     ![do i apply changes yes or no](https://github.com/user-attachments/assets/6dc44063-47fb-4ece-87b6-25e10335da9f)


  ### The  Jenkinsfile
     ---

The Jenkinsfile pipeline automates the process of code checkout, planning infrastructure changes with Terraform, and conditionally applying those changes. It's designed to ensure that changes to infrastructure (managed by Terraform) are reviewed and applied systematically, with an additional manual check before applying changes to critical environments like production.

Let's break down each section.

Pipeline
---

`pipeline { ... }`: This is the wrapper for the entire pipeline script. Everything that defines what the pipeline does is included within these braces.

Agent 
---
`agent any`: THis line specifies that the pipeline can run on any available agent. In Jenkins, an agent is a worker that executes the job. 'Any' means it doesn't require a specific agent configuration.

Environment
--- 
`environment { ... }`: This section is used to define environment variables that are applicable to all stages of the pipeline

`TF_CLI_ARGS = '-no-color'`: Sets an environment variable for Terraform. It tells Terraform to not colorize it's output, which can be used for logging and readability in CI/CD environments. Feel free to change this vaalue as you wish.

Stages
---
`stages { ... }`: This block defines the various stages of the pipeline. Each stage in the pipeline process.

Stages:Checkout
---

`stage('Checkout') { ... }`: This is the first stage, named Checkout. It's typically used to checkout source code from a version control system.

`checkout scm`: Checks out source code from the Source Control Management (SCM) system configured for the job (In this case, **Git**).

Stage: Terraform Plan
---

`stage('Terraform Plan') { ... }`: This stage is responsible for running a Terraform plan.

`withCredentials([aws(...)]) { ... }`: This block securely injects AWS credentials into the environment.

`sh 'terraform init'`: Initializes Terraform in the current directory.

`sh 'terraform plan -out=tfplan'`: Runs Terraform plan and outputs the plan to a file named tfplan.

Stage: Terraform Apply
---

`stage('terraform apply') { ... }`: This stage applies the changes from Terraform plan to make infrastructure changes.

`when { ... }`: This block sets conditions for executing this stage.

`expression { env.BRANCH_NAME == 'main' }`: This condition checks if the pipeline is running on the main branch.

`expression { ... }`: Checks if the build was triggered by a user action.

`input message: 'Do you want to apply changes?', ok: 'Yes'`: A manual intervention step asking for confirmation before proceeding. If *yes* is clicked, it runs the *"terraform init & apply"* otherwise, the pipeline is aborted.


![do i apply changes yes or no](https://github.com/user-attachments/assets/96317c32-8e86-4632-bc05-55a34c74b6f7)

## Enhancing and Extending the Pipeline

Objective
---

Update the existing pipeline script to include additional stages and improve the existing ones. This will encompass adding new functionality, improving code clarity and ensuring best practices in CI/CD pipelines.


1. Create a new branch from the main branch and scan the Jenkins pipeline so that the new branch can be discovered.

   ![created a new branch called refactor](https://github.com/user-attachments/assets/6c893b0d-a3d5-4a2c-8e5f-3a912fda8df9)

2. In the new branch, Correct and Enhance the 'Terraform Apply' Stage. It mistakenly contains a sh 'terraform aply -out=tfplan' command. Correct it to sh 'terraform apply tfplan'.


   ![before correction of terraform apply on jenkinsfile](https://github.com/user-attachments/assets/32a5b2c7-05b2-47c3-851e-f3371972e29a)


   ![corrected jenkins file terraaform apply](https://github.com/user-attachments/assets/bc583cfe-aeeb-4842-bc03-d1da406015fa)

   
3. Add logging to track the progress of the pipeline within both Terraform plan & apply. Use the echo command to print messages before and after each execution so that in the console output everyone can understand what is happening at each stage.


   ![echo terraform apply in jenkinsfile](https://github.com/user-attachments/assets/6d14147f-0a33-4068-86d8-72ffaa03e0e2)

4. Introduce a new stage in the pipeline script to validate the Terraform configurations using terraform validate. Add the 'Lint Code' Stage, Place this stage before the terraform plan stage. The purpose of this stage is to validate the syntax, consistency and correctness of Terraform configuration files in the directory it is run. It does not access any remote sevices.


   ![image](https://github.com/user-attachments/assets/4ca433de-f721-49a4-aeae-253a92b1beed)

5. Introduce a `cleanup` stage. Add a final stage named cleanup that runs regardless of whether previous stages succeeded or failed (use the post-directive). In this stage, include commands to clean up any temporary files or state that the pipeline may have created.Kindly find the link to the documentation on Jenkins Post Directive.

   ![image](https://github.com/user-attachments/assets/9c7d8891-bda4-4af4-b23a-b94830670ed3)

7. Implement Error Handling. Add error handling to the pipeline. For instance, if 'Terraform Apply' fails, the pipeline should handle this gracefully, perhaps by sending a notification or logging detailed error messages.
8. Document the Pipeline. Add comments to the pipeline script explaining each stage and important commands. This will make the pipeline more maintainable and understandable.

     



     





     


    


     

     

  
     



















       

       
       
    
       


     




         








