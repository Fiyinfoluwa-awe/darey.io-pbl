# AWS Networking Implementation Project (VPC, Subnets, Internet Gateways, NAT, Routing)

![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8b56d4a8-c6fa-4394-851b-90b8f23655c2)

## AWS VPC Creation and Subnet Configuration

Welcome to AWS Networking! This field uses Virtual Private Clouds (VPC's) and subnets to create the backbone of your cloud-based projects.

> What is an Amazon VPC?

An Amazon Virtual Private Cloud is like your very own private section of the Amazon cloud, where you can place and manage your resources (like servers or databases). You control who and what can go in and out, just like a gated community.

Essentially, there are 3 steps to creating a VPC and configuring core network services.

1) The Default VPC
2) Creating a new VPC
3) Creating and configuring subnets


 The Default VPC
---
The default VPC is like a  starter pack provided by Amazon for your cloud resources . It's a pre-configured space in the Amazon cloud where you can immediately start deploying your applications or servoces. 
It ha sna in-built security and network settings to help you get up and running quickly, but you can adjust these as you see fit.

A default VPC, which Amazon provides for you in each region ( think of a region as a seperate city), is like a pre-built house in that city. this house comes with some default settings to help you move in and start living
(or start deploying your applications) immediately. But just like a real house, you can change these settings according to you needs.

Creating a New VPC 
---
As we want to learn step by step and observe the components , choose the "VPC only" option, we'll use the "VPC and more" option later. Enter the "first-vpc " as nametag and "10.0.0.0/16" as the IPv4 CIDR . 
The '10.0.0.0/16" will be the primary IPv4 block and you can add secondary IPv4 block e.g., "100.64.0.0/16". Th use of case of secondary CIDR block could be because you are running out of IPs and need additional block, 
or there's a VPC with overlapping CIDR which you need to peer or connect.See this blog on how a secondary CIDR block is being used in an overlapping IP scenario: https://aws.amazon.com/blogs/networking-and-content-delivery/how-to-solve-private-ip-exhaustion-with-private-nat-solution/


![created my first Vpc](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6185d681-dae5-474b-94d5-bfe248c99794)

As soon as the VPC is created, it is assigned with a vpc-id and there's a route table created that serves as the main route table - rtb-0e030e362588ea8ad in the below screenshot.

![2 full view](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/45f9dda1-b144-4e09-bfb1-534a58e0f424)

