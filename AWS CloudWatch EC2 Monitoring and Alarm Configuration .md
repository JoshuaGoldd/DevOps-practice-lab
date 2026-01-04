# AWS CloudWatch EC2 Monitoring and Alarm Configuration project

## Overview

In this documentation, I showed how i configured AWS CloudWatch to monitor an EC2 instance and set up an automated alarm that sends email notifications when CPU utilization exceeds a defined threshold. I also generated CPU load on the instance to validate the monitoring and alerting workflow using real metrics.

---

## Launch an EC2 Instance

I launched an EC2 instance using an existing Launch Template to quickly provision the server.

**I followed the actions outlined below:**

* Navigated to **EC2 → Launch Templates**
* Selected the launch template
* Chose **Actions → Launch instance from template**
* Updated the key pair
* Assigned a unique instance name (`WEB01-CLOUDWATCH & WEB-CLOUDWATCH`) for easy identification
* Launched the instance

---

## Reviewed Default CloudWatch Metrics

After the instance was running, I reviewed its monitoring data.

**I followed the actions outlined below:**

* Opened **EC2 → Instances**
* Selected the instance
* Navigated to the **Monitoring** tab

**I Observed default metrics:**

* CPU Utilization
* Network In / Out (bytes)
* Network Packets In / Out
* CPU Credit Usage
* CPU Credit Balance

CloudWatch collects these metrics automatically every **5 minutes** by default.

![screenshot](\Images\CLD-1.png)

---

## Enable Detailed Monitoring

To collect metrics at a higher resolution, I enabled detailed monitoring.

**These were the actions i performed:**

* Clicked **Manage detailed monitoring**
* Enabled **Detailed Monitoring**
* Confirmed the change

**Result:**

* Metrics were collected every **1 minute**

---

## Connect to the EC2 Instance via SSH

I connected to the instance to generate CPU load.

**I followed the actions outlined below:**

* Allowed SSH access (port 22) from my IP in the security group
* Retrieved the instance public IP
* Connected using SSH

**Command used:**

```bash
ssh -i joshkey.pem ec2-user@<I pasted my PUBLIC_IP>
```

I confirmed the operating system:

```bash
cat /etc/os-release
```

![screenshot](\Images\CLD-2.png)

---

## Install the Stress Tool

To simulate CPU load, I installed the `stress` utility.

**Command used (since my operating system is Amazon Linux):**

```bash
sudo dnf install stress -y
```

*(For Ubuntu, this would be `apt update && apt install stress -y`.)*

![screenshot](\Images\CLD-3.png)

---

## Run Stress Commands to Generate CPU Load

I tested CPU stress manually to verify the tool.

**Command example:**

```bash
stress -c 4 -t 5
```

This command:

* Uses 4 CPU workers
* Runs for 5 seconds

To stop a running stress process:

```bash
Ctrl + C
```

---

## Create and Run a Stress Automation Script

To simulate real-world fluctuating load, I automated stress execution.

**I followed the actions outlined below:**

* Created a bash script (`stress.sh`)
* Added a loop to run stress at random intervals
* Made the script executable

**Commands used:**

```bash
vi stress.sh
chmod +x stress.sh
```

To run the script in the background:

```bash
nohup ./stress.sh &
```

This allowed the stress process to continue even after closing the SSH session.

---

## Verify CPU Load on the Instance

I monitored CPU usage locally.

**Command used:**

```bash
top
```

**Observation:**

* CPU utilization increased and decreased dynamically
* Load average fluctuated, simulating real server behavior

---

## Observe Metrics in CloudWatch

After allowing time for data collection, I reviewed CloudWatch graphs.

**I followed the actions outlined below:**

* Refreshed the **Monitoring** tab
* Switched graph view to **1-minute intervals**

**Observation:**

* CPU utilization spiked during stress execution
* Metrics reflected real-time workload changes

---

## Create a CloudWatch Alarm

I configured an alarm to trigger on high CPU usage.

**I followed the actions outlined below:**

* Navigated to **CloudWatch → Alarms → Create Alarm**
* Selected **EC2 → Per-Instance Metrics**
* Filtered metrics using the instance ID
* Selected **CPUUtilization**

---

## Configure Alarm Conditions

I defined the alarm threshold.

**Alarm configuration:**

* Metric: CPU Utilization
* Period: 1 minute
* Condition: **Greater than or equal to 60%**
* Evaluation: 1 consecutive period
* Alarm state: **ALARM**

---

## Configure SNS Email Notification

To receive alerts, I integrated the alarm with Amazon SNS.

**I followed the actions outlined below:**

* Created a new SNS topic:Warning
* Subscribed my email address: [noblejoshua12@gmail.com](noblejoshua12@gmail.com)
* Confirmed the subscription via email

![screenshot](\Images\CLD-4.png)

---

## Trigger and Validate the Alarm

I increased CPU stress to trigger the alarm.

**I followed the actions outlined below:**

* Modified the stress script to increase CPU workers
* Ran stress for longer durations

To identify and stop running stress processes:

```bash
ps -ef | grep stress.sh
kill <i pasted my PROCESS_ID after the command above>
```

**Outcome:**

* CPU utilization exceeded 60%
* Alarm state changed from **OK** to **ALARM**
* Email notification was successfully received
* CloudWatch graph showed sustained CPU spikes

---

## Cleanup

After completing the lab, I cleaned up all resources.

**I followed the actions outlined below:**

* Terminated the EC2 instance
* Deleted the CloudWatch alarm
* Verified no unused resources remained

---

## Key Takeaways

* CloudWatch provides automatic monitoring for AWS services
* Alarms eliminate the need for manual metric monitoring
* SNS enables real-time alerting
* Sustained high CPU usage is more critical than temporary spikes
* This setup forms the foundation for Auto Scaling, incident response, and automation

---
