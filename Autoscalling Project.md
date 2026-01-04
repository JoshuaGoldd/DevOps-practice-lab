# Hands-on Exercise Project

## Implementing an Auto Scaling Group with Application Load Balancer on AWS

---

## Objective

The objective of this exercise was to set up an **Auto Scaling Group (ASG)** in AWS that automatically manages EC2 instances for a web application.
The setup ensures:

* Instances are automatically created and replaced when unhealthy
* Traffic is evenly distributed using an **Application Load Balancer (ALB)**
* Instances remain **stateless** by storing shared data in **Amazon EFS**
* The infrastructure can scale in and out based on **CPU utilization**

---

## Overview of Required Components

Before creating the Auto Scaling Group, I identified the required components:

1. **Launch Template** – Defines instance configuration (AMI, instance type, key pair, security groups)
2. **Target Group** – Registers instances for load balancing
3. **Application Load Balancer** – Distributes incoming web traffic
4. **Auto Scaling Group** – Manages instance creation, replacement, and scaling

> Auto Scaling Groups can exist **with or without a load balancer**, but since this exercise serves web traffic from the internet, a load balancer was required.

---

## Create the Launch Template

I created a **new launch template** because the Auto Scaling Group relies entirely on it to decide:

* Which AMI to use
* Instance type
* Security groups
* Key pair

### Actions I Performed

* I Named the launch template: **HealthylivingShared**
* I Selected the **Helathyliving-EFS AMI** (an AMI connected to Amazon EFS)

  * Using an EFS-based AMI was optional, but I used it to demonstrate shared storage
* Instance type: **t2.micro / t3.micro**
* Key pair: **Healthyliving-key**
* Security groups:

  * Healthyliving security group
  * Load balancer security group
  * EFS security group
* I Added resource tags:

  * Name: **Healthyliving-Web**
  * Applied to instances, volumes, and network interfaces

After verifying all settings, I created the launch template successfully.

![screenshot](\Images\ATS-1.png)

---

## Create the Target Group

The target group acts as a **logical group of instances** that the load balancer forwards traffic to.

### Actions I Performed

* ICreated a new target group
* Name: **Healthyliving-ASG-TG**
* Target type: **Instances**
* Port: **80**
* Health check settings:

  * Protocol: HTTP
  * Port: 80
  * I Reduced healthy threshold to **2** so instances become healthy faster
* I Did **not add any instances manually**

> The Auto Scaling Group automatically adds and removes instances from the target group.

![screenshot](\Images\ATS-2.png)
---

## Create the Application Load Balancer

I created an **Application Load Balancer**. The application serves as a web traffic

### Actions i Performed

* Name: **Healthyliving-ASG-ALB**
* I Selected all Availability Zones

  * This allows instances to run in multiple zones for high availability
* I Removed default security group
* I Attached the **load balancer security group**
* Listener:

  * Protocol: HTTP
  * Port: 80
* I Forwarded traffic to the previously created target group

I waited until the load balancer status changed to **Active**.

![screenshot](\Images\ATS-3.png)
---

## Create the Auto Scaling Group

I created the Auto Scaling Group, with all prerequisites ready

### Basic Configuration i used

* Auto Scaling Group name: **Healthyliving-ASG**
* Launch template: **HealthylvingShared**
* VPC: Default VPC
* Availability Zones: I Selected all
* Instance placement strategy: **Balanced best effort**



---

## Load Balancer and Health Check Configuration

### Load Balancer Attachment

* I Enabled load balancing
* I Attached the Auto Scaling Group to the existing **target group**

### Health Checks

I enabled multiple health checks to ensure reliable instance replacement:

1. **EC2 Health Check**

   * Checks instance hardware and VM health
2. **Elastic Load Balancer Health Check**

   * Uses HTTP checks on port 80
   * Detects application-level failures (e.g., Apache crash)
3. **EBS Health Check**

   * Detects storage-related failures

> If an instance becomes unhealthy, the Auto Scaling Group removes it and launches a replacement.

---

## Capacity and Scaling Policies

### Desired Capacity

* Desired: **2 instances**
* Minimum: **1 instance**
* Maximum: **4 instances**

### Scaling Policy

* I Enabled **Target Tracking Scaling**
* Metric Used: **CPU Utilization**
* Target value used: **50%**

Behavior:

* CPU > 50% → Add instances
* CPU < 50% → Remove instances (down to minimum)

I kept scale-in enabled and did not disable instance termination.

---

## Maintenance, Monitoring, and Notifications

* Instance maintenance policy: Default behavior
* Did not enable scale-in protection
* Monitoring: Not enabled to avoid additional cost
* Notifications:

  * Launch
  * Terminate
  * Fail to launch
  * Fail to terminate

---

## Tagging

I added a **Name tag** to ensure instances launched by the Auto Scaling Group were easily identifiable:

* Name: **Healthyliving-Web-ASG**

---

## Verification and Testing

After creation, I waited a few minutes and verified:

* Two EC2 instances were running
* Both instances appeared as **Healthy** in the target group
* Load balancer DNS endpoint successfully served the website
* Images loaded correctly from **Amazon EFS**

This confirmed that:

* Load balancer
* Auto Scaling Group
* Target group
* EFS storage
  were all working together correctly.

![screenshot](\Images\ATS-4.png)

---

## Updating the Application (Launch Template Update)

To deploy updates:

* I updated the **launch template**
* I Selected a different version (without EFS)
* I Applied the changes

> Existing instances were not replaced automatically.

To fully apply changes, I performed an **Instance Refresh**:

* I Started instance refresh
* I Used “Terminate and launch at the same time” to control cost
* I Verified instance refresh history and events

---

## Advanced Auto Scaling Features Reviewed

I reviewed additional Auto Scaling capabilities:

* Instance standby mode
* Instance scale-in protection
* Dynamic scaling policies (step scaling)
* Predictive scaling (historical data-based)
* Scheduled scaling actions (e.g., traffic spikes during sales)

These features were explored for understanding but not fully implemented.

---

## Cleanup

To avoid unnecessary costs, I performed cleanup:

1. Deleted the **Application Load Balancer**
2. Deleted the **Auto Scaling Group**

   * Alternatively, capacity could be reduced to zero
3. Deleted unused **Target Groups**
4. Deregistered unused **AMIs**
5. Deleted unnecessary snapshots and volumes
6. Verified the AWS dashboard for leftover resources

---

## Final Outcome

At the end of this exercise, I successfully:

* Implemented an Auto Scaling Group with an Application Load Balancer
* Used a launch template to standardize instance configuration
* Ensured high availability across multiple Availability Zones
* Implemented application-level health checks
* Maintained a **stateless architecture** using Amazon EFS
* Practiced safe cleanup to avoid AWS costs

---

## Key DevOps Takeaway

This exercise demonstrated how **multiple AWS services integrate together**:

* EC2
* Auto Scaling
* Load Balancer
* Target Groups
* CloudWatch
* EFS

The setup follows DevOps best practices by ensuring:

* Automation
* Scalability
* Fault tolerance
* Stateless application design

---
