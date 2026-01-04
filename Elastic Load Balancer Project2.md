# Implementing an AWS Application Load Balancer with EC2 Instances

___

In this Project, I implemented an AWS Application Load Balancer (ALB) to distribute HTTP traffic across multiple EC2 instances. The goal was to simulate a real-world web application setup with high availability and proper traffic management.

___

## 1. Launch an EC2 Instances from a Launch Template

I began by launching two EC2 instances using an existing launch template. The template i used already contained the required Amazon machine image (AMI), instance type, security settings, and user data script for installing and running the web application.

From the Launch Templates section in EC2:

- I selected the template and launched two instances
- Both instances were created with identical configurations
- After launch, I renamed the instances to: web01 & web02
- These instances served as backend web servers running an HTTP service on port 80.

![screenshot](\Images\ELB2-1.png)
![screenshot](\Images\ELB2-2.png)
___

## 2. Create a Target Group

I created a target group, which defines where the load balancer forwards traffic and how instance health is monitored.

I completed the process using the following configuration:

- Target type: Instances, Protocol: HTTP, Port: 80
- Health check: Protocol: HTTP, Path: /, port: traffic port (80), Healthy threshold: 2, Unhealthy threshold: 2, Interval: 30 seconds, Timeout: 5 seconds, Success code: 200.
- I named the target group Healthyliving-TargetGroup and registered both EC2 instances (web01 and web02) on port 80.

___

## 3. Create an Application Load Balancer

I created an Application Load Balancer to handle incoming HTTP traffic.

Here was the configuration details i i used:

- Name: Healthyliving-Elasticloadbalance
- Scheme: Internet-facing
- IP address type: IPv4
- Availability Zones: All available zones selected
- Subnets: Default subnets

![screenshot](\Images\ELB2-3.png)
___

## 4. Configuring the Load Balancer Security Group

I created a dedicated security group for the load balancer to allow internet traffic.

- Inbound rules: HTTP (port 80) allowed from Anywhere (IPv4 and IPv6)
- I attached this security group to the load balancer and removed the default security group.

## 5. Listener and Routing Configuration

- I configured a listener on the load balancer: Listener protocol: HTTP, Listener port: 80, Default action: Forward traffic to **Healthyliving-TargetGroup**
- After completing the configuration, I created the load balancer and waited until its status became Active.
- I accessed the application using the DNS name of the load balancer but there was an error **504 gateway** from the website denying me acces into the EC2 severs.

This what i did next;

**Resolving Health Check Failures:**

Initially, both EC2 instances appeared as unhealthy in the target group.

Upon investigation, I identified the issue:

- The EC2 instance security group allowed HTTP traffic only from my IP
- The load balancer was not permitted to access the instances

**To fix this:**

- I edited the instance security group
- I Removed the rule allowing HTTP from my IP
- Added a new rule allowing HTTP (port 80) from the load balancer security group
- After a short wait, the health checks passed and both instances were marked healthy.

## 6. Verifying Load Balancer Access

Once the instances were healthy, I accessed the application using the DNS name of the load balancer the second time to acces the website from the server. The request was successfully routed to the backend instances, confirming that:

- The load balancer was working correctly
- Traffic was being distributed to healthy instances

___

**Cleanup and Resource Management:**

After completing the exercise, I cleaned up the environment:

- I Terminated both EC2 instances
- I Deleted the application load balancer
- I Deleted the target group
