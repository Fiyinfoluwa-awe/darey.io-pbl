# MONITORING INFRASTRUCTURE IN AWS (Cloudwatch, Cloud trail)

## Introduction to Monitoring Infrastructure in AWS
Imagine just launching your startup's new application on Amazon Web Service AWS. Excitedly you see traffic start to flow in as users begin to interact with your service. However, with success comes responsibility. As your application grows, so does the complexity of managing it's underlying infrastructure. How do you that everything continues to run smoothly? How do you detect and address issues before they impact your users? This is where monitoring your infrastructure becomes crucial.
Monitoring, refers to the process of obsevring and collecting data about the perfoormance, health, and behaviour of systems, applications, networks, or infrastructure components. The primary goal of monitoring is to ensure that these systems operate effectively, efficiently, and securely, while also detcting and addressing any issues or anomalies in a timely manner.

## AWS CloudWatch and Cloud Trail

AWS CloudWatch is a monitoring and observability service provided by Amazon Web Service AWS. It allows users to collect and track metrics, monitor logs, set alarms, and automatically react to changes in AWS resources and applications running on the AWS infrastructure. CloudWatch provides insights into the performance, health, and operational status of AWS resources and applications, helping users to troubleshoot issues, optimize resource utilization and ensure the reliability of their systems.

AWS CloudTrail on the other hand is also a service provided by Amazon Web Services (AWS) that enables governance, compliance, operational auditing, and risk auditing of your AWS account. CloudTrail records and logs all API activity in your AWS account, providing a comprehensive trail of events that can be used for security analysis, resource change tracking, troubleshooting, and compliance auditing.

### CloudWatch Metrics and Alarms
---

Amazon CloudWatch Metrics and Alarms are essential components of the Amazon CloudWatch service, which provides monitoring and observability capabilities for AWS resources and applications. Let's delve into each of these components;

**CloudWatch Metrics**
CloudWatch Metrics are data points representing the behavior of AWS resources and applications over time. These metrics can be collected from various AWS services such as Amazon EC2, Amazon RDS, Amazon S3, AWS Lambda, and many others. Metrics provide insights into the performance, health, and operational status of these resources, allowing users to monitor and analyze their behavior.

![image](https://github.com/user-attachments/assets/bf4000ce-c2f6-489f-87fb-4f8ab3f384ed)



**Key aspects of CloudWatch Metrics include:**

**Default and Custom Metrics:** AWS services automatically publish default metrics to CloudWatch, such as CPU utilization, network traffic, and disk I/O for EC2 instances. Additionally, users can create custom metrics to monitor specific aspects of their applications or services.

**Namespace and Dimensions:** Metrics are organized into namespaces, which categorize related metrics together. Within each namespace, metrics can have dimensions that further specify the resource or aspect being monitored. For example, an EC2 instance metric might have dimensions such as Instanceld or InstanceType.

**Timestamps and Units:** Each metric data point includes a timestamp indicating when the measurement was taken, as well as a unit specifying the measurement's scale (e.g., bytes, percentage, seconds).

![cloud metrics cover page](https://github.com/user-attachments/assets/4cfdac30-91b3-44f1-8cd5-f1ca6096de78)

**Retention and Granularity:** CloudWatch retains metric data for different periods depending on the data's age and granularity. Users can specify the granularity of their metric data, ranging from one-minute to one-day intervals.

![image](https://github.com/user-attachments/assets/38719045-cd05-449d-ba68-54ba38aba236)

**CloudWatch Alarms**
CloudWatch Alarms allow users to define thresholds on CloudWatch Metrics and trigger actions when these thresholds are breached. Alarms are used to proactively monitor the health and performance of AWS resources and applications, enabling users to respond promptly to changes in their environment.

**Key aspects of CloudWatch Alarms include:**

**Thresholds and Actions:** *Users can set thresholds on CloudWatch Metrics, specifying conditions that, when met or exceeded, trigger alarm states. When an alarm enters an alarm state, users can configure actions such as sending notifications via Amazon SNS, executing AWS Lambda functions, or auto-scaling resources.*

**Alarm States:** *CloudWatch Alarms have three possible states: OK, INSUFFICIENT_DATA, and ALARM. The OK state indicates that the metric is within the defined threshold, while the ALARM state indicates that the threshold has been breached. The INSUFFICIENT_DATA state occurs when there is not enough data to evaluate the alarm.*

**Alarm History:** *CloudWatch maintains a history of alarm state changes, allowing users to track when alarms transition between states and investigate the circumstances surrounding each state change.*

**Configuration and Management:** *Users can create, modify, and delete alarms through the CloudWatch Management Console, AWS CLI, or SDKs. Alarms can be managed individually or as part of larger monitoring configurations, such as CloudFormation templates or AWS Auto Scaling policies.*

## Monitoring AWS EC2 using CloudWatch

Now that we have an idea of what AWS CloudWatch and CloudTrail is all about, let's launch an EC2 instance and monitor it.

**Step 1:** *Create an IAM Role With CloudWatchFullAccess and SSMFullAccess*

* Navigate to the IAM console.
* In the IAM Console navigation click on roles.

  ![step 1 roles page](https://github.com/user-attachments/assets/3904834b-f37a-49ca-a335-7b7611e1bee8)

* Follow the images below to create a role with `CloudWatchFullAccess` and `SSMFullAccess` policy;

  ![step 2 creating roles](https://github.com/user-attachments/assets/7d4c47e0-55bb-4f90-bf0d-561d05916089)

  ![step 2d creating roles cloudwatch permission](https://github.com/user-attachments/assets/bd3d3d29-e4ca-4ef4-8d45-9ad6da8b659c)

  ![step 2d creating roles ssm full acess permission](https://github.com/user-attachments/assets/59237520-0ded-418a-8ffb-fc3e1fb9e595)

  ![step 2e creating roles  name and review](https://github.com/user-attachments/assets/29ca0d2c-4953-4abf-b821-bcb3b47905ea)

  ![step 2e creating roles  name and review 2](https://github.com/user-attachments/assets/cd1d57a9-d6e2-4326-90e0-867de23d73d3)

  ![step 2e creating roles  name and review 3](https://github.com/user-attachments/assets/8200761a-6da4-4c0e-afbf-280210e6fbbd)

  **Step 2:** *Create a parameter in System Manager*

Now that we have created an IAM role, we need to create a parameter in the system manager console. By doing this, we will be able to define the metrics we want to monitor for our EC2 instance.

1. Navigate to the AWS System Manager Console.
2. In the AWS System Manager navigate manu, select parameter store.

![step 4 creating parameters](https://github.com/user-attachments/assets/e7ac31d7-f5af-45c4-abd7-42b674114cb3)

![step 4b creating parameters system manager](https://github.com/user-attachments/assets/7616b38a-ecd7-4c0f-918b-bab6a7717e8e)

3. Create  a new parameter and paste the code snippet below

     ```
   {
	"metrics": {
		"append_dimensions": {
			"InstanceId": "${aws:InstanceId}"
		},
		"metrics_collected": {
			"mem": {
				"measurement": [
					"mem_used_percent"
				],
				"metrics_collection_interval": 180
			},
            "disk": {
				"measurement": [
                     "disk_used_percent"
				],
				"metrics_collection_interval": 180
			}
		}
	}
}
    ```




![step 4c creating parameters](https://github.com/user-attachments/assets/5cfee873-e369-4c3c-9ca2-3ae0e7b105c5)

Pasted the code snippet in the `Value`

![step 4d creating parameters](https://github.com/user-attachments/assets/dae63ab0-1c40-422d-b466-57e3e3f7447c)

![step 4e created parameters](https://github.com/user-attachments/assets/89a7354d-d2c4-4ac8-94e0-1cff309ffc93)


The parameters above are a configuration file for the CloudWatch agent, which defines the metrics that will be collected from your EC2 instance and sent to CloudWatch.

1.**"metrics":** This is the top-level key in the configuration file, indicating that it contains the definitions for the metrics to be collected.

2. **"append_dimensions":** This section specifies dimensions to be appended to all collected metrics. Dimensions are key-value pairs that help identify the source of the data in CloudWatch. In this case, the dimension Instanceld is appended, and its value is populated dynamically with the instance ID of the EC2 instance where the CloudWatch agent is installed.

    *  **"InstanceId":** "${aws: InstanceId}": This line specifies that the value of the Instanceld dimension should be dynamically populated with the instance ID of the EC2 instance.
      
3. **"metrics_collected":** This section defines the specific metrics to be collected from the EC2 instance.

     * **"mem":** This subsection specifies memory-related metrics to be collected.

          *  **"measurement":** This is an array of specific memory metrics to collect. In this case, only "mem_used_percent" is specified, which represents the percentage of memory used on the instance.

          * **"metrics_collection_interval":** This parameter specifies how frequently (in seconds) the metrics should be collected. Here, memory metrics will be collected every 60 seconds.

     * **"disk":** This subsection specifies disk-related metrics to be collected.

          * **"measurement":** This is an array of specific disk metrics to collect. Only "disk_used_percent" is specified, representing the percentage of disk space used on the instance.

          * **"metrics_collection_interval":** Similar to the memory section, this parameter specifies how frequently disk metrics will be collected, which is every 60 seconds.
      



## Create an Ec2 Instance, Attach the role created in Step 1

Now that we have created an IAM Role and also created a parameter in the Account System Manager Console, let's create an EC2 instance for that roles we created earlier. But note that SSM will have access to the parameter we created and by attaching the role to the EC2 instance, EC2 will also have access to the parameters.

  1. Navigate to the EC2 console, select instances. Click on launch instance on the top right

  2. Now we will need to launch an `Amazon linux 2 instance` and attch the role created in `step 1`. Follow the images below to attach IAM role to the created instance.

     ![step 5b creating an instance](https://github.com/user-attachments/assets/12b61ea6-cde6-4f4f-bda8-bd5edd14d42b)

     ![step 6 attaching roles to the instance](https://github.com/user-attachments/assets/6a761035-0362-4951-947d-c69402f0b8eb)

     ![step 6b attaching roles to the instance](https://github.com/user-attachments/assets/f1d8c871-954b-4f86-bad9-048ae79a7b8a)

  3. Install CloudWatch agent. Create a file name `script.sh` and paste the shell script below.

       * Create a file

         `sudo vi script.sh`

       * Paste the shell script below
    


 ```
         #!/bin/bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgent.zip
unzip AmazonCloudWatchAgent.zip
sudo ./install.sh
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/alarm/AWS-CWAgentLinConfig -s
```





* Make the file executable 

  `sudo chmod +x script.sh`
        
* save and run the file with;

   `./script.sh`

        
        
  ![step 8 chmod script sh](https://github.com/user-attachments/assets/60fd80eb-610e-41e2-9b40-cf87bf667b9d)


4. Start the CloudWatch agent

   `sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a start`

   ![step 9 starting the cloudwatch ](https://github.com/user-attachments/assets/88f911ea-ed75-4fa0-bba3-a8ff94919dbb)


6. Verify if the CloudWatch is installed and running successfully

   `sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status`

   ![step 10 status of cloudwatch running](https://github.com/user-attachments/assets/f170a582-ef6b-4183-af36-5503e04ba857)


**Step 4:** *Monitor Your Metric in CloudWatch*
---

Before we can monitor our EC2 instance metrics, create a new policy and attach it to our IAM role so that the role does not lack permissions to perform the ec2:DescribeTags action, which is necessary for the CloudWatch agent to retrieve EC2 instance tags.

  1. Create a new Policy

     * In the IAM console navigation menu, click on policy and on the top right, select create policy and attach it to the IAM role. I will be using the JSON code snippet below for my policy.
    

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeTags"
            ],
            "Resource": "*"
        }
    ]
}

```


![step 11 creating a new policy](https://github.com/user-attachments/assets/2d1807ad-e416-4a1e-a394-f63f5b421fcf)

![step 11b creating a new policy scriot of the policy](https://github.com/user-attachments/assets/bb08f7bf-026e-49f0-ad6c-707ad6bff89f)

![step 11c creating a new policy](https://github.com/user-attachments/assets/de06a6ed-c693-44b5-b3f2-227c65a5c121)

![step 11d creating a new policy](https://github.com/user-attachments/assets/1fd873aa-9572-485c-8f9a-8d6f9c737ea8)

![step 12 policy created](https://github.com/user-attachments/assets/c0e79222-976f-4ae9-869e-dbee62bb3330)

![step 13 attaching policy to role](https://github.com/user-attachments/assets/c6a6b9a2-c89b-4db3-9815-c79dbecbd061)

![step 13b attaching policy to role adding permissions](https://github.com/user-attachments/assets/ef74744f-8b59-4b11-b65a-50235b6af461)

![step 14 policy successfully attached ](https://github.com/user-attachments/assets/665cec17-ffa3-4d01-9c99-42b0b8f59290)


2. Let's recall the parameters we created for our EC2 metric, now let's view the metric on CloudWatch console.
   
    * Navigate to the CloudWatch console. In the navigation menu, select all metrics.
  
      ![step 15 cloudwatch all metrics](https://github.com/user-attachments/assets/ff1a2992-257c-43ca-bb35-524b588dde27)

    * Select the browser tab and *search* and *click* on **"CWAgent"**
  
      I had error at first
  
      ![step 16 error with Cw agent search](https://github.com/user-attachments/assets/daf8dbd6-a1d8-4ba6-8c08-9e878c1dc3a9)

      after another trial

      ![step 16b later brought out cwagent](https://github.com/user-attachments/assets/b968258c-2a3e-4537-be2f-6a84ed35fbfa)

   * We can view our metric the memory percent of out EC2 instance.

![step 17 the end](https://github.com/user-attachments/assets/3017938e-b2f4-4d55-8aef-e31b1a034682)

![step 17b the end](https://github.com/user-attachments/assets/d04e4091-683b-4f15-b0ef-d70583f3f0d3)

COMPLETED! We have successfully installed and configured CloudWatch to monitor our EC2 instance. To monitor more metrics, you can go to the parameter store and edit the parameter we created then add more parameters to it. Follow the AWS Official Documentation to read more on parameters syntax for metrics.



     


      











   


   















  

  




  










