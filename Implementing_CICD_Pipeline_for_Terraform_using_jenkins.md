# IMPLEMENTING CICD PIPELINE FOR TERRAFORM USING JENKINS

I will be implementing  a seamless CI/CD pipeline for Terraform using Jenkins int this project, enabling automated testing, deployment, and management of infrastructure-as-code with ease.

## Introduction to CI/CD and its importnave in software development

In today's rapidly evolving IT landscape, efficient and reliable deployment of infrastructure is paramount. Continuous Integration and Continuous Deployment (CI/CD) have emerged as indispensable practices, fostering automation and agility in the software development lifecycle. In this project, we will explore the powerful combination of Terraform, a leading Infrastructure as Code (IaC) tool, and Jenkins, a widely used automation server, to streamline and enhance infrastructure deployment workflows.

### Project Overview
---

In the journey ahead, we will delve into the intricacies of building a robust CI/CD pipeline specifically tailored for Terraform projects. By automating the building, testing, and deployment of infrastructure changes, these pipelines enhance speed, reliability, and consistency across environments. The use of Infrastructure as Code (laC) with Terraform ensures reproducibility and scalability, while Jenkins facilitates collaborative development, visibility, and continuous integration and deployment as you will see in this project. This approach not only reduces time-to-market but also promotes resource efficiency, cost optimization, and compliance with security standards. Overall, CI/CD pipelines with Terraform and Jenkins empower organizations to adapt quickly to changing business requirements, fostering a culture of automation and continuous improvement in the dynamic landscape of modern software development and operations.

### Setting up the Enviroment 
---

I started by setting up a Jenkins server running in a docker container. Then will create a Dockerfile to define the configuration for our Jenkins server. This Dockerfile will include the necessary dependencies and configurations to run Jenkins seamlessly, and also to run terraform cli.

*Dockerfile for Jenkins*

***Note:** Jenkins comes with a docker image that can be used out of the box to run a container with all the relevant dependencies for Jenkins. But because we have unique requirements to run terraform, we need to find a way to extend the readily available jenkins image.*

1) Created  an EC2 instance , make sure that it is a `t2.medium` or run Docker Desktop. In my cae, I used an EC2 instance.

![signing into the instance](https://github.com/user-attachments/assets/a44204bb-49ce-4469-8d02-4b3df84f347a)

2) Next, I need to have Docker running in my instance or Dockerdesktop. To install Docker in my instance , I ran

`sudo apt-get install -y docker -ce`

![step 2 sudo apt install docker -ce](https://github.com/user-attachments/assets/bb3d4d45-3788-4063-af69-ddf13224fec2)

3) Now that Docker is installed, verify that it is installed properly with `sudo systemctl status docker`

![step 3 sudo systemctl status docker](https://github.com/user-attachments/assets/c4af2fbf-3412-40dd-ba1a-9b7862177f24)





