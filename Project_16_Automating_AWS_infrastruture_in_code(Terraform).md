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

```
# Create public subnet1
resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-1a"

}

# Create public subnet2
resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-1b"
}
```

![updated the main tf file , adding public and private subnets](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bb29c11a-82cd-4245-a457-05075172a3d8)

We are creating 2 subnets, therefore declaring 2 resource blocks - one for each of the subnets.

We are using the `vpc_id` argument to interpolate the value of the VPC `ID` by setting it to `aws_vpc.main.id`. This way, Terraform knows inside which VPC to create the subnet.

> run the command `terraform plan` and `terraform apply`

![terraform plan subnets ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/54e7dfcd-3160-49e6-bd52-579fb1129e91)

![terraform plan subnets 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/c95a0df9-262c-4ced-88a5-e694420084c1)

![terraform apply](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f983f467-6be0-4011-a615-fc4415f6ef43)

![terraform apply 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/51085a6c-6b70-4217-9d82-7e7db92bee4a)

![vpc created by terraform](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d89d2066-64fa-4460-9fca-c79178c6c50b)

![subnets created by terrraform](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/15cc4967-97c2-4902-95fb-9dedeea099f4)

_**Observations:**_

* Hard coded values: Remember our best practice hint from the beginning? Both `availability_zone` and `cidr_block` arguments are hard coded. We should always endeavour to make our work dynamic.

* Multiple Resource Blocks: Notice that we have declared multiple resource block for each subnet in the code. This is bad coding practice. We need to create a single resource block that can dynamically create resources without specifying multiple blocks. Imagine if we wanted to create 10 subnets, our code would look very clumsy. We need to optimize this by introducing a `count` argument.

_Let us improve our code by refactoring it._

First, destroy the current infrastructure. Since we are still in development, this is totally fine._ Otherwise, **DO NOT DESTROY** an infrastructure that has been deployed to production. To destroy whatever has been creates run:

> `terraform destroy` and type `yes` after evaluating the plan.

![terraform destroy](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/eb11d456-32b2-47cf-9b27-ac6d97b8fddf)

![terraform destroy complete](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/152755bb-497e-4d0d-b159-2385ca6c1947)


## Fixing the problem by Code Refactoring

* **Fixing Hard Coded Values:** We will introduce variables, and remove hard coding.

    - Starting with the provider block, declare a variable named `region`, give it a default value and update the provider section by referring to the declared variable.

    ```
          variable "region" {
        default = "us-east-1"
    }

    provider "aws" {
        region = var.region
    }
    ```

- Do the same to `cidr` value in the `vpc` block, and all other arguments.

  

    ```
    variable "region" {
        default = "us-east-1"
    }

    variable "vpc_cidr" {
        default = "172.16.0.0/16"
    }

    variable "enable_dns_support" {
        default = "true"
    }

    variable "enable_dns_hostnames" {
        default ="true" 
    }

    variable "enable_classiclink" {
        default = "false"
    }

    variable "enable_classiclink_dns_support" {
        default = "false"
    }

    provider "aws" {
    region = var.region
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = var.vpc_cidr
    enable_dns_support             = var.enable_dns_support 
    enable_dns_hostnames           = var.enable_dns_support
    enable_classiclink             = var.enable_classiclink
    enable_classiclink_dns_support = var.enable_classiclink

    }
    ```

* **Fixing multiple resource blocks:** This is where things become a little tricky. We are going to introduce **Loops & Data Sources**.


Terraform has a functionality that allows us to pull data which exposes information to us. For example, every region has Availability Zones (AZ). Different regions have from 2 to 4 Availability Zones. With over 20 geographic regions and over 70 AZs served by AWS, it is impossible to keep up with the latest information by hard coding the names of AZs. Hence, we will explore the use of Terraform's Data Sources to fetch information outside of Terraform. In this case, from AWS

Let us fetch Availability zones from AWS, and replace the hard coded value in the subnet's `availability_zone` section.


  ```
         # Get list of availability zones
        data "aws_availability_zones" "available" {
        state = "available"
        }
```

To make use of this new `data` resource, we will need to intoduce a `count` argument in the subnet block: Something like this; 

  

```
    # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```


To unnderstand what's going on here;

- The `count` tells us that we need 2 subnets. Therefore, Terraform will invoke a loop to create 2 subnets.

- The `data` resource will return a list object that contains a list of AZ's. Internally, Terraform will receive the data like this:
  


        ["eu-central-1a", "eu-central-1b"]

  

Each of them is an index, the first one is index `0`, while the other is index `1`. If the data returned had more than 2 records, then the index numbers would continue to increment.

Therefore, each time Terraform goes into a loop to create a subnet, it must be created in the retrieved AZ from the list. Each loop will need the index number to determine what AZ the subnet will be created. That is why we have `data.aws_availability_zones.available.names[count. index]` as the value for `availability_zone`. When the first loop runs, the first index will be `0`, therefore the AZ will be `eu-central-1a`. The pattern will repeat for the second loop.

But we still have a problem. If we run Terraform with this configuration, it may succeed for the first time, but by the time it goes into the second loop, it will fail because we still have `cidr_block` hard coded. The same `cidr_block` cannot be created twice within the same VPC. So, we have a little more work to do.


### Let's make `cidr_block` dynamic.

    
We will introduce a function `cidrsubnet()` to make this happen. It accepts 3 parameters. Let us use it first by updating the configuration, then we will explore its internals.



```
    # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```



![edited public subnet 1 , introduced count](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5b1d4875-459f-4f01-b7bf-34e62bc453b5)


A closer look at `cidrsubnet` - this function works like an algorithm to dynamically create a subnet CIDR per AZ. Regardless of the number of subnets created, it takes care of the cidr value per subnet. 

It's parameters are `cidrsubnet(prefix, newbits, netnum)`.


- The `prefix` parameter must be given in CIDR notation , same as for VPC.
- The `newbits` parameter is the number of additional bits with which to extend the prefix. For example, if given a prefix ending with /16 and a newbits value of 4, the resulting subnet address will have a length /20
- The `netnum` parameter is a whole number that can be represented as a binaryinteger with no more than `newbits` binary digits, which will be used to populate the additional bits added to the prefix   



We can experiment how this works by entering the `terraform console` and keep changing the values to see the output.

- On the terminal, run `terraform console`
- type `cidrsubnet("172.16.0.0/16", 4, 0)`
- Hit Enter
- View the output
- Keep changing the numbers and see what happens.
- To exit the console, type `exit`


![terraform console cidr](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4b5059af-0be3-4d64-8386-6dfc0a4e6913)


## **The final problem to solve is removing the hard coded `count` value.**

If we cannot hard code a value we want, then we will need a way to dynamically provide the value based on some input. Since the `data` resource returns all the AZs within a region, it makes sense to count the number of AZs returned and pass that number to the `count` argument.

To do this, we can introduce `length()` function, which basically determines the length of a given list, map or string.

Since `data.aws_availability_zones.available.names[count.index]` returns a list like `["eu-central-1a", "eu-central-1b", "eu-central-1c"]`

Open up `terraform console` and try it.

![image](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/55c89691-5809-4249-8da9-436de28e47b4)

Now we can update the public subnet block like this:

```
# Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }
```


_**Observations:**_

* What we have now is sufficient to create the subnet resource required but if you observe, it does not satisfy our business requirement of just `2` subnets. The `length` function will return number 3 to the `count` argument, but what we actually need is `2`.

Let us fix this:

* Declare a variable to store the desired number of public subnets and set the default value.
  

```
  variable "preferred_number_of_public_subnets" {
      default = 2
}
```

* Next, update the `count` argument with a condition. Terraform needs to check first if there is a desired number of subnets. Otherwise, use the data returned by the `length` function. See how that is presented below.


```
# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}
```

Let us break down the above code:

* The first part `var.preferred_number_of_public_subnets == null` checks if the value of the variable is set to `null` or has some value defined.

* The second part `?` and `length(data.aws_availability_zones.available.names)` means, if the first part is true, then use this. In otherwords, if preferred number of public subnets is `null` _(Or not known)_ then set the value to the data returned by `length` function.

* The third part `:` and `var.preferred_number_of_public_subnets` means, if the first condition is false, i.e preferred number of public subnets is `not null` then set the value to whatever is defined in `var.preferred_number_of_public_subnets`.

Now the entire configuration should now look like this below:

```
# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = 2
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}


# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}

```

**Note:** You can also try changing the value of `preferred_number_of_public_subnets` variable to`null` and notice how many subnets get created.

## Variables & tfvars

Introducing variables.tf & terraform.tfvars
---

Instead of having a long list of variables in main.tf, we can actually make our code a lot more readable and better structured by moving out some parts of the configuration content to other files.

* We will put all variable declarations in a separate file
* And provide non default values to each of them.

1) Create a new file and name it `variables.tf`

2) Copy all the variable declarations into the new file.

3) Create another file, name it `terraform.tfvars`

4) Set values for each of the variables.

**The resulting code will look like this now:**

**main.tf**

```
# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}
```

![final script of main tf](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/acb640fe-e32b-43b1-a983-78b72b37eb43)

![final script of main tf 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ffc4fd51-3e59-49c7-b1ea-57fb6834494d)

**variables.tf**

```
variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = null
}
```

![created variables tf files and moved the variables](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/8b9e1433-be4c-4e2b-a7e8-97d0c20de8ff)

**terraform.tfvars**

```
region = "us-east-1"

vpc_cidr = "172.16.0.0/16" 

enable_dns_support = "true" 

enable_dns_hostnames = "true"  

enable_classiclink = "false" 

enable_classiclink_dns_support = "false" 

preferred_number_of_public_subnets = 2
```


The structure in the PBL folder should look like this;


```
└── PBL
    ├── main.tf
    ├── terraform.tfstate
    ├── terraform.tfstate.backup
    ├── terraform.tfvars
    └── variables.tf
```


![my final structure ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/781f52d0-009c-4e6c-9927-3c881873d7f8)

![created terraform tfvars](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/aba7d809-24fc-4e43-bc31-8b98482cc31d)

Then ran `terraform plan` and ensure everything runs fine

![terraform fmt almost at the end](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0ee53257-5beb-48b3-9723-40055db3ca19)



