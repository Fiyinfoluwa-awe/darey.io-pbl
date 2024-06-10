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

Now I have a VPC and a route table, but I won't be able to put anything inside. If I try to create and EC2 instance for example, I can't proceed as it requires subnets.

Creating and Configuring Subnets
---

_What are Subnets?:_
Subnets are like smaller segments within a VPC that help you organize and manage your resources. Subnets are like dividing an office building into smaller sections, whwere each section represents a department. In this analogy, subnets are created to organize and manage the network effectively.

![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1d0c1973-e495-411d-a955-eea904bd36f9)


| Subnet name | AZ | CIDR Block |
| :---: | :---: | :---:|
| subnet-public1a | us-east-1a | 10.0.11.0/24 |
| subnet-public2b | us-east-1b | 10.0.12.0/24 |
| subnet-private1a | us-east-1a | 10.0.1.0/24 |
| subnet-private2b | us-east-1b | 10.0.2.0/24 |

Go to VPC > Subnets > Create Subnets and select the VPC that you have created previously.

![creating subnets](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7c949c85-4a51-4742-aabe-70f70547b175)

![subnet 1](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9250c7f5-956d-4cfb-b344-ce0f789b08ed)

Enter the subnet setting detail. Don't click the **"create subnet"** button just yet, click the **Add new subnet** button to add the remaining subnets then after completing all the required subnets, `create subnet`. **Note:** if you don't choose a zone , it will be randonly picked by AWS.

I was done creating the subnets, which can be seen on the console. If you missed any, just create a subnet and select your desired VPC. As of now, you can deploy EC@ instances into the VPC  by selecting one of the subnets, but the public subnet doesn't have any internet access at this stage. When you select a public subnet > route, you'll see it uses the main route table and only has the local route for internet access. 


![subnets created successfully](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8028e3af-bd9c-4fbb-86d1-f00bfaadbd12)

Understanding Public and Private Subnets in AWS VPC 
---

In the world of AWS VPC, think of subnets as individual plots in your land (VPC). Some of these plots (subnets) have direct road access (internet access) - these are public subnets. Others are more private, tucked away without direct road access - these are private subnets.

Creating a Public Subnet 
---
Creating a private subnet is like creating a secluded plot without direct road (internet) access. Here's how you do it:

* Go to the AWS VPC page.
* Find 'Subnets', click on it, then click 'Create subnet'. Give this new plot a name, select the big plot (VPC) you want to divide, and leave the IP settings as they are.
* Don't attach an Internet Gateway to this subnet, keeping it secluded.
* The route table for this subnet doesn't allow direct traffic to and from the internet.


Creating a Private Subnet 
---

* Go to the AWS VPC page.
* Find 'Subnets', click on it, then click 'Create subnet'.
* Give this new plot a name, select the big plot (VPC) you want to divide, and leave the IP settings as they are.
* Attach an Internet Gateway to this subnet to provide the road (internet) access.
* Update the route table associated with this subnet to allow traffic to flow to and from the internet.

  
Working with Public and Private Subnets
---

Public subnets are great for resources that need to connect to the internet, like web servers. Private subnets are great for resources that you don't want to expose to the internet, like databases.

Understanding public and private subnets helps you to organize and protect your AWS resources better. Always remember, use public subnets for resources that need internet access and private subnets for resources that you want to keep private.

Introduction to Internet Gateway and Routing Table 
---

Just like in a real city, in your virtual city (VPC), you need roads (Internet Gateway) for people (data) to come and go. And you also need a map or GPS (Routing Table) to tell people (data) which way to go to reach their destination.

>What is an Internet Gateway?

An Internet Gateway in AWS is like a road that connects your city (VPC) to the outside world (the internet). Without this road, people (data) can't come in or go out of your city (VPC).

Deep Dive into Internet Gateways 
---
to give your public subnet access to the main road (internet), you need an Internet Gateway. This acts like the entrance and exit to your property. We'll show you how to create and attach an Internet Gateway to your VPC.

Public Subnets
---

Technically, the subnets are still private. You'll need these to make it work as public subnets:

* An Internet Gateway (IGW) attached to the VPC
* Route table with default route towards the IGW
* Public IP assigned to the AWS resources (e.g., EC2 instances)

Go to VPC > Internet gateways and click **"Create internet gateway"**, Insert a name tag and click **"create internet gateway"**

![internet gateways](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4039272c-ad76-4762-b3e7-b304d16558e4)

![creating internet gateways](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/932dad91-0c39-48c5-a1f2-81d09504322b)




