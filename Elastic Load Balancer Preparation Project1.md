# Elastic Load Balancer Preparation: EC2 Instance and AMI Setup

___

For this project, I prepared an environment to later configure an Elastic Load Balancer (ELB). The goal was to have one or more web server instances running a sample website, and to create a reusable AMI for rapid deployment of additional instances.

___

The following tasks were carried out as part of this project:

___

## 1. Launch the EC2 Instance

I completed the process using the following steps:

- I began by launching an EC2 instance to host the website:
- I chose Amazon Linux 2023 as the operating system and selected the T2 Micro instance type.
- I created a new key pair for secure SSH access and downloaded it locally.
- For security, I configured a security group allowing SSH (port 22) and HTTP (port 80) access from my IP address.
- In the Advanced Details → User Data, I uploaded a multi-OS setup script provided in the lecture resources. This script automatically installed and configured the "Inner Peace" website.
- After launching the instance, I verified that the website was running by accessing its public IP in a web browser.

___

## 2. Create an AMI (Amazon Machine Image)

- To enable rapid deployment of additional instances, I created an AMI from the running EC2 instance.

- I selected the instance and navigated to Actions → Image and Templates → Create Image.
- I named the AMI **Healthylivingmage** and allowed the instance to reboot during the image creation.
- The AMI creation process generates a snapshot of the EBS volume along with the instance metadata, allowing this instance to be replicated accurately.
- I monitored the AMI creation under the AMIs section until its status changed to available. The corresponding snapshot was also automatically created

![screenshot](\Images\ELB-1.png)
___

## 3. AMI Management

I explored additional AMI management options for future use:

- Copying AMI to another region: This allows deployment of identical instances in different regions.
- Sharing AMI with other AWS accounts: By adding an AWS account ID, the AMI can be shared across accounts.
- Deregister and deleting AMIs: If an AMI is no longer needed, it can be deregistered and its associated snapshot deleted.

___

## 4. Launch Instance from AMI

Using the created AMI, I launched additional instances to simulate a cluster of web servers behind a load balancer. I configured these instances with the required key pair, security group, and instance type.

___

## 5. Launch Templates

I created a Launch Template:

All necessary configuration details (AMI ID, instance type, key pair, security groups, etc.) were saved in the template.

This allows instances to be launched quickly from the template, ensuring consistency across multiple instances and simplifying cluster management.

![screenshot](\Images\ELB-2.png)
![screenshot](\Images\ELB-3.png)
___

**Result:**

I successfully deployed a web server instance, created a reusable AMI, and prepared a launch template for rapid replication. These steps provide the foundation for configuring an Elastic Load Balancer, enabling scalable and highly available web applications.

___
