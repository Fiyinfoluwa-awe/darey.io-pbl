# Automating AWS Infrastructure in Code with Terraform
---

![t](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ce2ae987-0306-4246-8a82-dedd849b93ef)

### Automate Infrastructure with IaC using Terraform Part I
--- 

I have built several AWS infrastructures over the course of my previous projects. Now I will be automating the process of spinning up server infrastructure using Terraform.

I will be leveraging on the power of Infrastructure-as-code (Iac) to build similar setups.

![Aws Multiple Infrastructure](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7526bb05-dac0-44a7-9b89-be79e7325fb1)

### Pre-requisites before we begin writing Terraform code

1) Terraform course completed

2) IAM user created for programmatic access with Administrator Access

![step 1creating IAM user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0420e4ab-af9d-4bce-a093-6a4d797b02e6)

![setting permissions for the IAM user](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c4a83928-53cd-445a-8b19-6dab668b6779)

![review of IAM user ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c7d84af0-3876-4691-88e2-368cdf52dd50)

![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4def66ba-abe4-4808-b383-1bb4fdba79cc)

3) Access key ID and secret access key ID created and saved to notepad.

![click on create access key ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/01a252c8-8b31-420f-a168-ec7e60f04116)

![creating access key CLI](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e24dfedb-d33d-4b00-87bf-1d1bd36b7802)

![access key created ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c592d63f-8997-4b27-a06f-9ed3138530a0)

4) Installed Python SDK Boto3 by reading the instructions. This will confirm python 3.8 or later is available, if not you have to install it. Then install Boto3 by running the command pip install boto3[crt].

![pip version, pip install boto3(crt)](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/25a68df4-1683-49ce-9a23-c2eb42abc064)

Upgraded the pip utility since I was notified of an update.

![upgrade pip ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/aea5465f-1fc3-44db-992c-ede7c760b517)

5) Configured programmatic access from my workstation to connect to AWS using the access keys copied in 3 above using the command aws configure.

![aws configure](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8564701e-1a34-4521-b9c6-042e3c8f7db1)

6) Created an s3 bucket Amazon Simple Storage Service to store terraform state file. My bucket is named `dherby-dev-terraform-bucket`

![s3 bucket successfully created](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5d6b3e6a-6d59-4a07-949a-338a9143b597)

7) After configuring authentication and installing boto3, confirmed if I can programatically access my AWS account by running the below commands in

`>python`

```
# I used the windows terminal so I typed 'python' to enter python

> python

you can also type in `py` instead of `python`

# And paste in the below code

import boto3
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```

I should see my previously created s3 bucket name. In my case `dherby-dev-terraform-bucket`

![import boto3 , had to press enter to print it](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/af0c8b09-b7a0-4e32-8e4c-329df49a4dd5)

## The secrets to writing quality Terraform code
---

The secret recipe of a successful terraform project consists of:

1) Your understanding of your goal (desired AWS infrastructure end state)
2) Your knowledge of the IaC technology used (in my case - Terraform)
3) Your ability to effectively use up-to-date Terraform documentation _[here](https://developer.hashicorp.com/terraform/language)_

As we proceed with this project, we will get familiar with [Terraform-specific-technology](https://developer.hashicorp.com/terraform/docs/glossary) such as:

* [Attribute](https://developer.hashicorp.com/terraform/docs/glossary#attribute)
* [Resource](https://developer.hashicorp.com/terraform/docs/glossary#resource)
* [Interpolations](https://developer.hashicorp.com/terraform/docs/glossary#interpolation)
* [Argument](https://developer.hashicorp.com/terraform/docs/glossary#argument)
* [Providers](https://developer.hashicorp.com/terraform/docs/glossary#terraform-provider)
* [Provisioners](https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax)
* [Input Variables](https://developer.hashicorp.com/terraform/docs/glossary#input-variables)
* [Output Variables](https://developer.hashicorp.com/terraform/docs/glossary#output-values)
* [Module](https://developer.hashicorp.com/terraform/docs/glossary#module)
* [Data Source](https://developer.hashicorp.com/terraform/docs/glossary#data-source)
* [Local Values](https://developer.hashicorp.com/terraform/language/v1.1.x/configuration-0-11/locals)
* [Backend](https://developer.hashicorp.com/terraform/docs/glossary#backend)

Other concepts to know include `data types` like `Integer`, `Float`, `String`, `Boolean` etc.

```
Best Practices

- Ensure that every resource is tagged using multiple keyvalue pairs.
- Try to write reusable code, avoid hard coding values wherever possible.
```

## Base Infrastructure Automation (VPC | Subnets | Security Groups)

Created a directory structure `Terraform > PBL > main.tf` to the empty file `main.tf` as shown below;

![created PBL and main tf](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dac4d843-1957-43a6-9114-33e67817cf78)


Installed terraform CLI by following [this instruction](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

* Added `AWS` as a provider, and a resource to create a VPC in the `main.tf` file.

* Provider block informs Terraform that we intend to build infrastructure within AWS.

* Resource block will create a VPC.


Pasted in the code below :

    ```
    provider "aws" {
    region = "us-east-1"
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = "172.16.0.0/16"
    enable_dns_support             = "true"
    enable_dns_hostnames           = "true"
    #enable_classiclink             = "false"
    #enable_classiclink_dns_support = "false"
    }
    ```

![first issue](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3422ed03-ce2b-4a6d-b11c-2faab668d880)


**Note** : You should  change the region in the code to your actual region 


* Download necessary plugins for Terraform to work. These plugins are used by `providers` and `provisioners`. At this stage, we only have `provider` in our `main.tf` file. So Terraform will download only plugin for AWS provider.

* Ran the command `terraform init` as seen below:

![terraform init](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/84154850-f6c1-4e65-b47b-49695af5a531)

the `terraform init` didn't work as it should (it didn't create a lock file named `.terraform.lock.hcl`). So I had to edit the code in the `main.tf` file, removing the last two lines and then run it again;

 ```
    provider "aws" {
    region = "us-east-1"
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = "172.16.0.0/16"
    enable_dns_support             = "true"
    enable_dns_hostnames           = "true"
    }

 ```

![terraform init finally worked edited the main tf file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9bfec051-fcdf-4039-b01a-1eb679e6b4ef)

* _Notice the new `.terraform\...` directory that is created. This is where Terraform keeps plugins. Generally, it is safe to delete this folder. It just means that you must execute `terraform init` again to download them._

* Now we can create the only resource we just defined. `aws_vpc`. But first we should check to see what terraform intends to create before we tell it to go ahead and create it. To do this run the command `terraform plan` and if you are happy with the planned changes the execute:

 > `terraform apply`.

![ran terraform plan](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/aaf4226f-b143-4612-8a68-86df454e13f1)

**Observations:**

1) A new file is created `terraform.tfstate`. This is how Terraform keeps itself up to date with the exact state of the infrastructure. It reads this file to know what already exists, what should be added, or destroyed based on the entire terraform code that is being developed.

2) If you also observed closely, you would realise that another file gets created during planning and apply. But this file gets deleted immediately. `terraform.tfstate.lock.info` This is what Terraform uses to track who is running its code against the infrastructure at any point in time. This is very important for teams working on the same Terraform repository at the same time. The lock prevents a user from executing Terraform configuration against the same infrastructure when another user is doing the same - it allows to avoid duplicates and conflicts.

Its content is usually like this below. More on this later.


```
    {
        "ID":"e5e5ad0e-9cc5-7af1-3547-77bb3ee0958b",
        "Operation":"OperationTypePlan","Info":"",
        "Who":"dare@Dare","Version":"0.13.4",
        "Created":"2020-10-28T19:19:28.261312Z",
        "Path":"terraform.tfstate"
    }
```

Its a `json` format file that stores information about a user's `ID`, what operation he/she is doing, timestamp and location of the `state` file.

## Refactoring bad practice

### Subnets resource section

According to our architectural design, we require 6 subnets: 2 public, 2 private for webservers and 2 private for data layer. Let us create the first 2 public subnets.

Add the below configuration to the main.tf file:
