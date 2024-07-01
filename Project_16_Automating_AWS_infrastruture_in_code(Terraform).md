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
# I used the windows terminal so I typed 'py' to enter python

> py

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
3) Your ability to effectively use up-to-date Terraform documentation here
