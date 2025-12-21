# AWS Systems Manager (SSM) & CloudShell Project

___

This lab documents demostrate the use of AWS Systems Manager and AWS CloudShell to achieve secure, scalable server management and AWS resource administration without traditional SSH access or local CLI setup.

___

The scope of this project included the following tasks:

1. Accessing EC2 Without SSH Using AWS Systems Manage
2. Creating an IAM Role for AWS Systems Manager
3. Attaching the IAM Role to the EC2 Instance
4. Accessing the Instance Using Session Manager
5. Running Commands on One or Multiple Instances Using Run Command
6. Open and run command on  AWS CloudShell

___

## 1. Accessing EC2 Without SSH Using AWS Systems Manager

___

**Objective:** To Launching an EC2 Instance

___

To begin this lab, I launched an EC2 instance that would later be managed using AWS Systems Manager.

I followed the steps outlined below:

Step 1: Navigate to EC2

- From my AWS Management Console, I opened EC2
- I selected Instances and clicked Launch Instance

Step 2: Configure Instance Details

- I assigned my instance name as web01
- I selected Ubuntu Server 24, which is Free Tier eligible, as my Amazon Machine Image (AMI)
- I chose t2.micro (or t3.micro) as my instance type to stay within the Free Tier limits

Step 3: Key Pair Configuration

- I selected my existing key pair (a new key pair could also be created)
- Since access would be handled through AWS Systems Manager, this key pair was not required for SSH access

Step 4: Security Group Selection

- I selected my existing security group (or created a new one as needed)
- No inbound SSH rules were required because SSH access would not be used

Step 5: Launch the Instance

- After reviewing the configuration, I scrolled down and clicked Launch Instance

- EC2 instance was successfully created and ready to be managed securely using AWS Systems Manager instead of traditional SSH access.

___

## 2. Creating an IAM Role for AWS Systems Manager

___

**Objective:** To allow my EC2 instance to communicate securely with AWS Systems Manager, I created an IAM role with the required permissions.

I followed the steps outlined below:

Step 1: Navigate to IAM Roles

- From my AWS Management Console, I opened IAM
- I selected Roles and clicked Create Role

Step 2: Select Trusted Entity

- I chose AWS service as my trusted entity type
- I selected EC2 as my use case and clicked Next to continue

Step 3: Attach Required Policy

- In the policy search bar, I searched for **AmazonSSMManagedInstanceCore**
- I selected this policy, which allows EC2 instances to register and communicate with AWS Systems Manager and clicked Next

Step 4: Name and Create the Role

- I assigned role with the name SSM-Managed-Instance-Core
- I added a brief description to clarify the role’s purpose
- I reviewed my configuration and clicked Create Role
- The IAM role was successfully created and ready to be attached to an EC2 instance to enable Systems Manager functionality.

___

## 3. Attaching the IAM Role to the EC2 Instance

___

After creating the IAM role, I attached it to the EC2 instance **web001** to enable communication with AWS Systems Manager.

I followed the steps outlined below:

Step 1: Open the EC2 Instances Page

- From the AWS Management Console, I navigated to **EC2** and then to **Instances**
- I selected the EC2 instance **web001** I previously launched

Step 2: Modify the IAM Role

- I clicked Actions and selected Security and lastly i selected Modify IAM role

Step 3: Attach the Role

- From the IAM role dropdown, I selected the role I created earlier
- I clicked **Update IAM** role to apply my changes
- EC2 instance was granted the required permissions to connect securely to my AWS Systems Manager without using SSH keys.

___

## 4. Accessing the Instance Using Session Manager

___

After attaching the IAM role, I accessed the EC2 instance securely using AWS Systems Manager Session Manager.

I followed the steps outlined below:

Step 1: Open Systems Manager

- From my AWS Management Console, I navigated to **AWS Systems Manager**

Step 2: Start a Session

- I selected **Session Manager** from the left-hand menu and I clicked Start session

Step 3: Select the Instance

- I waited a few moments for the instance to show on my page
- I Verified that the correct IAM role was attached to the instance
- I selected the target EC2 instance
- I clicked Next, then Start session

Step 4: Access the Shell

- At this time a new browser tab opened, providing shell access to my EC2 instance
- From this session, I was able to run administrative commands directly on the server without using SSH keys or opening inbound SSH ports.
- I run the followuing command

```powershell
sudo -i
cat /etc/os-release
sudo apt update
```

## 5. Running Commands on One or Multiple Instances Using Run Command

___

After confirming shell access through Session Manager, I used AWS Systems Manager Run Command to execute commands on the EC2 instance without logging in interactively.

___

I followed the steps outlined below:

step 1: Open Run Command

- From AWS Systems Manager, I navigated to Run Command and clicked Run command

step 2: Select the Command Document

- From the list of available documents, I selected **AWS-RunShellScript**

NB: This document allows execution of Linux shell commands on one or multiple instances

step 3:Enter Commands

- In the Commands section, I entered the commands I wanted to run, which are:

```powershell
ls -l /var/log
sudo apt update
```

NB: Multiple commands can be added, or an entire shell script can be executed.

step 4: Choose Target Instances

- I selected my target EC2 instance manually from my list
(Alternatively, instances can be targeted using tags or resource groups)

1. Execute the Command

- I clicked Run, my command execution status changed from In Progress to Success

step 6: Review Output

- I selected my instance to view the command output. Standard output appeared under Output

This approach allows centralized command execution across one or many servers, making it ideal for tasks like updates, configuration changes, or maintenance at scale.

## 6. Open and run command on  AWS CloudShell

___
This is a quick AWS CLI access without setup

I followed the steps outlined below:

- I Clicked the CloudShell icon in my AWS console

- A terminal opens automatically which already confirms that i am logged in using my AWS account permissions
- I run the commands bellow:

```powershell
aws ec2 describe-instances
aws s3 ls
```
