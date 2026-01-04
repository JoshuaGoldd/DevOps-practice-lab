# AWS Elastic Block Store (EBS) – Hands-On Lab Documentation

___

**Overview:**

In this lab, I worked with Amazon Elastic Block Store (EBS), which provides persistent block storage for EC2 instances. EBS volumes act like virtual hard disks attached to servers, allowing operating systems, applications, and data to be stored reliably.

___
I started by reviewing what EBS is and how it works. EBS provides block-level storage for EC2 instances, similar to a physical hard disk on a traditional server. Each EC2 instance uses an EBS volume as its root volume to store the operating system, and additional EBS volumes can be attached for application or data storage. I also learned that EBS volumes are created within a specific Availability Zone (AZ) and must be attached to instances in the same AZ. The data on EBS volumes is automatically replicated within the AZ for durability.

Next, I reviewed the different EBS volume types, including:

- General Purpose SSD (gp3/gp2) for most workloads
- Provisioned IOPS SSD for high-performance databases
- Throughput Optimized HDD for big data and log processing
- Cold HDD for infrequently accessed data
- Magnetic volumes for low-cost archival use

___

## **TASK:**

___

### 1. Launch an EC2 Instance

___
**The following steps describe what I did:**

I launched an Amazon Linux 2023 EC2 instance with the default 8 GB gp3 root EBS volume. I used a user-data script to automatically install and configure an Apache web server hosting a sample website. After the instance was running, I connected to it via SSH and verified:

- The Apache service was running
- Website files existed under `ls /var/www/html`
- Images were stored in `ls /var/www/html/images`

Using Linux commands such as `fdisk -l` and `df -h`, I confirmed that the instance had only one EBS volume attached, which was mounted to the root (/) directory.

![screenshot](\Images\image14.png)
___

### 2. Create and Attach a New EBS Volume

___
The requirement was to create an additional 5 GB storage volume specifically for the website’s image files. This would ensure better storage organization and demonstrate how to attach and mount extra EBS volumes.

**The following steps describe what I did:**

I created a new 5 GB gp3 EBS volume in the same Availability Zone as the EC2 instance. I tagged the volume with a meaningful name to indicate that it was used for web image storage. After the volume was created, I attached it to the EC2 instance using the device name /dev/sdf. Once attached, I verified the new disk on the server using `fdisk -l`
___

### 3. Create a Partition

___
I used the `fdisk` utility to create a new primary partition on the attached volume and Allocated the full 5 GB to my partition. I Saved changes to create `/dev/xvdf1` knowing that this step prepares the disk for formatting.

![screenshot](\Images\image-15.png)
![screenshot](\Images\image-16.png)
___

### 4. Format the Partition

___
I formatted the partition using the ext4 filesystem. ext4 is a stable, general-purpose Linux filesystem suitable for web data and application storage. At this stage, the disk was ready to be mounted.

![screenshot](\Images\image17.png)
![screenshot](\Images\image-18.png)
___

### 4. Mount the Volume (Temporary Mount)

___
Before mounting the new volume, I backed up the existing image files to a temporary directory to avoid data loss. I then mounted the new partition to /var/www/html/images.
After mounting:

- I copied the image files back into the directory
- Restarted the Apache service
- Verified that the website loaded correctly and all images displayed as expected

This confirmed that the images were now being served from the new EBS volume

___

### 5. Configuring Persistent Mount

___
I configured a permanent mount by editing the /etc/fstab file since the mount would be lost after a reboot.I added an entry specifying:

- The partition path
- The mount point
- The filesystem type (ext4)
- Default mount options

I validated the configuration using mount -a and confirmed the volume was mounted correctly with `df -h`
___

**Conclusion:**

In this project, I successfully:

- Created and attached an additional EBS volume to an EC2 instance
- Partitioned and formatted the volume
- Mounted it to a specific directory used by a web application
- Configured persistent mounting using /etc/fstab

This hands-on lab helped me understand how EBS integrates with EC2, how Linux manages disks and partitions, and how DevOps engineers separate application data from root volumes for better scalability and management.

___
