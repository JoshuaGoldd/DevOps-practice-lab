# Amazon EFS (Elastic File System) Project

## Objective

I aimed to:

* Understand what a shared file system is and why it’s important.
* Create and mount an Amazon EFS file system on my Linux EC2 instance.
* Use EFS as shared storage for my web server’s images.
* Explore security groups, NFS, and access points in AWS.

---

## Introduction

I learned that Amazon EFS is a **shared file system** that allows multiple servers to access the same data.

On my website, if users upload images and the load balancer routes their request to a different server, the uploaded files would not be available if I only used block storage (like EBS). That’s why I needed **centralized shared storage**.

EFS is cloud-managed and works over NFS, so I didn’t have to set up my own NFS server.

---

## The following tasks were carried out as part of this project

---

### Creat a Security Group for EFS

1. I went to **EC2 → Security Groups → Create Security Group**
2. I Named it `	Healthyliving-EFS-SG`
3. I Added an inbound rule:

* **Type:** NFS
* **Port:** 2049
* **Source:** Security group of my web server (`Healthyliving-SG`)

1. I saved the security group.

![screenshot](\Images\EFS-1.png)
![screenshot](\Images\EFS-2.png)

---

### Create EFS File System

1. I went to **EFS → File Systems → Create File System**
2. Named it `Healthyliving-EFS-Images`
3. Selected **Regional** file system type
4. Disabled automated backups and lifecycle management
5. Enabled **encryption at rest**
6. Set performance mode to **General Purpose** and throughput mode to **Bursting**
7. Removed default security groups from subnets and attached my **EFS security group**
8. Clicked **Create File System** and waited until it became **Available**

![screenshot](\Images\EFS-3.png)

---

### Create EFS Access Point

1. I went to **EFS → Access Points → Create Access Point**
2. I Selected my EFS file system (`Healthyliving-EFS-Images`)
3. I Clicked **Create Access Point**

This access point will allow my EC2 instance to mount the EFS securely.

![screenshot](\Images\EFS-4.png)

---

### Launch EC2 Instance (Web Server)

1. I went to **Launch Templates → Healthyliving-Template → Launch Instance**
2. SSH’ed into my instance:

```bash
ssh -i Healthyliving-key.pem ec2-user@<my PUBLIC_IP is pasted here>
sudo su
```

---

### Prepare Web Server Folder

1. I Navigated to my website folder:

```bash
cd /var/www/html
```

2. I Moved existing images to a temporary folder:

```bash
mkdir /tmp/images/
cd /var/www/html/images
mv images/* /tmp/images/
systemctl restart httpd
```

![screenshot](\Images\EFS-5.png)
![screenshot](\Images\EFS-6.png)
3. Verified in the browser that the images were gone.

> I did this because I wanted `/var/www/html/images` to be the mount point for my EFS.

---

### Install EFS Utilities

I installed the EFS client on Amazon Linux 2023:

```bash
yum install -y amazon-efs-utils
```

> On other Linux distributions, I would use the appropriate package manager (APT, etc.)

---

### Mount EFS File System

1. I copied the **File System ID** and **Access Point ID** from the EFS console.
2. Edited fstab:

```bash
vim /etc/fstab
```

3. I Added this line (replacing `<FS_ID>` and `<AP_ID>` with my values):

```Powershell
<FS_ID>:/ /var/www/html/images efs _netdev,tls,accesspoint=<AP_ID> 0 0
```

1. I Mounted the file system:

```bash
mount -a
df -h
```

The `/var/www/html/images` folder was now mounted to EFS.

![screenshot](\Images\EFS-7.png)

---

### Restore Images

1. I run the command

```bash
cp -r /var/www/html/temp_images/* /var/www/html/images/
systemctl restart httpd
```

* Verified in the browser that the images were now coming from EFS.

> The website images were no longer on the local disk but in the shared EFS storage.

![screenshot](\Images\EFS-7.png)

---

### Create AMI with EFS Mount

1. I went to **EC2 → Instances → Actions → Create Image**
2. I Named it `Healthyliving-EFS`
3. This AMI included the EFS mount point so future instances would automatically connect to EFS.

![screenshot](\Images\EFS-8.png)

---

* After the AMI became available, I deleted the EC2 instance.
* I kept the EFS and access point because they are free under the AWS free tier (<5GB).

---

## Result

* My EC2 instance was successfully connected to **Amazon EFS**.
* The images folder was **shared storage**, accessible from multiple instances.
* EFS mounted automatically at boot using fstab.
* I now have a **centralized shared storage** solution for my web server.

---
