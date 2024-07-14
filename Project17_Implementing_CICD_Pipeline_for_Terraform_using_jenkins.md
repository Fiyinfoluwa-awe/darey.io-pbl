# IMPLEMENTING CICD PIPELINE FOR TERRAFORM USING JENKINS

I will be implementing  a seamless CI/CD pipeline for Terraform using Jenkins int this project, enabling automated testing, deployment, and management of infrastructure-as-code with ease.

## Introduction to CI/CD and its importnave in software development

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

Therefore, the first logical thing to have is an existing terraform code. 

if you dont have your own code, you can simply use this github link and fork the respository into your own github account https://github.com/dareyio/terraform-aws-pipeline.git ; It creates Networking later , also - It provisions kubernetes cluster using EKS

Then do the following to test that the code can create existing resources




         








