# EBS Snapshots Lab

___

In this documentation, I explored EBS snapshots to understand how backups, recovery, and data migration work in AWS.

___

Understanding Snapshots:

I learned that EBS snapshots are backups of EBS volumes. The first snapshot captures the full volume, while subsequent snapshots are incremental, saving only changes made after the previous snapshot. Snapshots can also be used to:

- Restore lost or corrupted data
- Change volume type or size
- Move data across Availability Zones or Regions
- Share data between AWS accounts
- Encrypt previously unencrypted volumes

___

## 1. Launch an EC2 Instance

___
**The following steps describe what I did:**

I launched an Amazon Linux 2023 EC2 instance with the default 8 GB gp3 root EBS volume. I used a user-data script to automatically install and configure an Apache web server hosting a sample website. After the instance was running, I connected to it via SSH and verified:

- The Apache service was running
- Website files existed under `ls /var/www/html`
- Images were stored in `ls /var/www/html/images`

Using Linux commands such as `fdisk -l` and `df -h`, I confirmed that the instance had only one EBS volume attached, which was mounted to the root (/) directory.

___

## 2. Create a New Database Volume

___

I created a new EBS volume for database storage.

**Steps I followed:**

- I created a 5GB gp3 EBS volume in the same Availability Zone as the instance.Tagged it as db01-mysql-vol. Attached it to the instance. Renamed the instance logically for database usage

- On the server, I Verified the new disk using `fdisk -l`. I created a primary partition, formatted it using XFS, created the directory /var/lib/mysql and mounted the partition to `/var/lib/mysql`

![screenshot](\Images\Ebssnapshot-3.png)
___

## 3. Install MariaDB on Separate Storage

I installed the database service using `dnf install mariadb105-server -y`. Before starting the service, the /var/lib/mysql directory was empty. After starting MariaDB, I confirmed that database files were automatically created inside /var/lib/mysql, proving that database data was now stored on the separate EBS volume.

![screenshot](\Images\Ebssnapshot-2.png)
___

## 4. Take a Snapshot

- With live database data present, I created an EBS snapshot of the volume:
- Named and tagged the snapshot
- Waited for the snapshot to complete
- This snapshot now served as a backup of the database volume.

___

## 5. Simulate Data Loss

To test recovery:

- I deleted the contents of /var/lib/mysq
- Restarted MariaDB
- I confirmed the service failed due to missing data

![screenshot](\Images\Ebssnapshot-2.png)
___

## 6. Restore from Snapshot

To recover corrupted volume or deleted volume from Snapshot: I

- unmounted the corrupted volume
- Detached and deleted it
- Created a new EBS volume from the snapshot
- Kept the same Availability Zone
- Attached the recovered volume to the instance
- Mounted it back to /var/lib/mysql

After mounting:

- All database files were restored
- MariaDB started successfully
- The service returned to a healthy running state

![screenshot](\Images\Ebssnapshot-3.png)
___

During the lab, I confirmed that snapshots can be used to:

1. Restore data quickly
2. Resize or change volume type (e.g., gp3 → provisioned IOPS)
3. Encrypt existing data
4. Move volumes across AZs or Regions
5. Share data across AWS accounts.

This lab demonstrated how EBS snapshots provide reliable backup, recovery, and flexibility for production workloads such as databases. I now understand how to safely protect data, recover from failures, and migrate storage across environments using snapshots

___
