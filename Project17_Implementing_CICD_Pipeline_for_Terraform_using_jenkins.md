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

1) **Downloading HashiCorp's GPG Key:**
  
   `wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg`

  * This command downloads HashiCorp's GPG key from `https://apt.releases.hashicorp.com/gpg`

  * `gpg --dearmor` converts the key into a format that can be easily used by tools.

  * `tee /usr/share/keyrings/hashicorp-archive-keyring.gpg` writes the key to the specified file with `.gpg` extension.

2) Displaying the GPG key fingerprint:

`gpg --no-default-keyring --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg --fingerprint`

  * This command displays the fingerprint of the GPG key. The fingerprint is a unique identifier for the key and is used to verify its authenticity.

3) Confuguring the repository with the GPG key:

   `echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list`

    * This command adds HashiCorp's repository configuration to the system's package manager (`apt` in this case).
    * It specifies the GPG key to be used for package verification.

4) Updating and installing Terraform:
   `RUN apt-get update && apt-get install -y terraform && rm -rf /var/lib/apt/lists/*`

   * These commands update the package list, install Terraform, and then remove unnecessary packages lists to reduce the size of the Docker image

5) **WORKDIR /app:** This line sets the working directory to /app. this is where you will enter the container.
6) **RUN terraform --version:** this command prints the version of Terraform to the console , allowing you to verify that the installation was successful.
7) **USER jenkins:** this command switches the jenkins user returning to a lower privelege level. This is good for security practice to minimize the risk of running proccesses as the root user within the container.

  ## Building and Running the Docker Image

  Make sure that you are inside the folder containing the Dockerfile. The content of the build is sent to the Docker







