---
title: "Week 3 Worklog"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

- Learn the overview of Amazon EC2 and its role in Cloud infrastructure.
- Practice creating VPCs and Security Groups for Linux and Windows instances.
- Launch, connect to, and manage EC2 instances on AWS.
- Become familiar with basic EC2 operations such as modifying Instance Type, creating Snapshots, creating AMIs, and recovering access.
- Deploy the AWS User Management application on both Linux and Windows Server.
- Practice cost governance and permission control using IAM Policies.
- Clean up resources after completing the lab to avoid unnecessary charges.

---

### Tasks Planned for This Week:

| Day | Tasks | Start Date | Completion Date | Reference |
| --- | --- | --- | --- | --- |
| Monday | - Read the Introduction to Amazon EC2 section <br> - Understand the workshop overview and hands-on architecture <br> - Prepare AWS account, Region, Key Pair, and connection environment | 04/05/2026 | 04/05/2026 | https://000004.awsstudygroup.com/ |
| Tuesday | - Module 2.1: Create a Linux VPC <br> - Module 2.2: Create VPC for Windows Instance <br> - Module 2.3: Create Security Group for Linux Instance <br> - Module 2.4: Create Security Group for Windows Instance | 05/05/2026 | 05/05/2026 | https://000004.awsstudygroup.com/ |
| Wednesday | - Module 3.1: Launch Microsoft Windows Server 2022 Instance <br> - Module 3.2: Connect from Computer to Windows Instance <br> - Verify Remote Desktop and Windows key pair login | 06/05/2026 | 06/05/2026 | https://000004.awsstudygroup.com/ |
| Thursday | - Module 4.1: Launch Amazon Linux Instance <br> - Module 4.2: Connect to Amazon Linux Instance <br> - Practice SSH connection to Linux Instance using key pair | 07/05/2026 | 07/05/2026 | https://000004.awsstudygroup.com/ |
| Friday | - Module 5.1: Modify EC2 Instance Type <br> - Module 5.2: Create and Manage EBS Snapshots <br> - Module 5.3: Create Custom AMI <br> - Module 5.4: Launch Instance from Custom AMI <br> - Module 5.5 - 5.7: Recover access and Remote Desktop to Ubuntu | 08/05/2026 | 08/05/2026 | https://000004.awsstudygroup.com/ |
| Saturday | - Module 6: Deploy AWS User Management Application on Amazon Linux <br> - Install LAMP Server, configure database, phpMyAdmin, Node.js, and deploy the application | 09/05/2026 | 09/05/2026 | https://000004.awsstudygroup.com/ |
| Sunday | - Module 7: Deploy Node.js Application on Windows EC2 <br> - Module 8: Cost & Usage Governance with IAM <br> - Module 9: Clean up resources | 10/05/2026 | 10/05/2026 | https://000004.awsstudygroup.com/ |

---

### Week 3 Achievements:

#### Knowledge Gained

**Amazon EC2 Overview**

- Understood Amazon EC2 as a scalable cloud computing service.
- Learned how EC2 supports flexible infrastructure deployment.
- Explored the use of Linux and Windows servers on AWS.

**AWS Networking Fundamentals**

- Learned how to create and configure VPCs.
- Understood the relationship between VPC, Subnet and Security Group.
- Practiced configuring inbound rules for:
  - SSH (`22`)
  - RDP (`3389`)
  - HTTP (`80`)
  - HTTPS (`443`)

**Connecting to EC2 Instances**

- Used `.pem` key pair files for Linux SSH authentication.
- Decrypted Windows administrator password using key pairs.
- Connected successfully to:
  - Windows Server through Remote Desktop
  - Amazon Linux through SSH

**EC2 Administration**

- Modified EC2 Instance Types.
- Created and managed EBS Snapshots.
- Created Custom AMIs from configured instances.
- Launched new EC2 instances from Custom AMIs.

**Application Deployment on AWS**

- Installed and configured Apache Web Server.
- Configured PHP and Database services.
- Installed phpMyAdmin for database management.
- Installed Node.js and deployed sample applications.

**IAM Governance and Cost Management**

- Practiced using IAM Policies to restrict AWS resource usage.
- Applied governance rules based on:
  - AWS Region
  - Instance Type
  - IP conditions
  - Time-based conditions
- Learned the importance of governance in cloud environments.

---

#### Hands-on Practice

**Module 2 — Preparation**

* Create a VPC for the Linux Instance.
* Create a VPC for the Windows Instance.
* Create a Security Group for Linux:
* Allow SSH from a personal IP.
* Allow HTTP if running a web server is needed.


* Create a Security Group for Windows:
* Allow RDP from a personal IP.
* Do not open `0.0.0.0/0` unless necessary.



**Module 3 — Launch Microsoft Windows Server 2022 Instance**

* Select the Microsoft Windows Server 2022 AMI.
* Choose an Instance Type suitable for the Free Tier or lab.
* Assign a Key Pair to retrieve the password.
* Configure the Security Group to allow RDP.
* Launch the Windows EC2 Instance.
* Decrypt the password using the key pair file.
* Connect to the Windows Instance using Remote Desktop.

**Module 4 — Launch Amazon Linux Instance**

* Select the Amazon Linux AMI.
* Choose an appropriate Instance Type.
* Assign a Key Pair.
* Configure the Security Group to allow SSH.
* Launch the Linux EC2 Instance.
* Connect to the instance via SSH using a terminal.

**Module 5 — Amazon EC2 Basic**

* Practice changing the Instance Type.
* Create and manage an EBS Snapshot.
* Create a Custom AMI from a configured EC2 Instance.
* Launch a new EC2 Instance from the Custom AMI.
* Learn how to restore access to a Windows Instance.
* Learn how to restore access to a Linux Instance.
* Practice using Remote Desktop into an Ubuntu EC2.

**Module 6 — Deploy AWS User Management Application on Amazon Linux**

* Install a LAMP Web Server.
* Verify that Apache/PHP is working.
* Configure the database server.
* Install phpMyAdmin.
* Install Node.js on Amazon Linux.
* Deploy the AWS User Management application.
* Test the basic CRUD functions of the application.

**Module 7 — Deploy Node.js Application on EC2 Windows**

* Install XAMPP on the Windows Instance.
* Install Node.js on the Windows Instance.
* Deploy the AWS User Management Application on the Windows Server.
* Test the application via a web browser.

**Module 8 — Cost & Usage Governance with IAM**

* Create an IAM Policy to limit service usage by Region.
* Create a Policy to limit EC2 by Instance Family.
* Create a Policy to limit EC2 by Instance Type.
* Create a Policy to manage allowed EBS Volume types.
* Create a Policy to restrict resource deletion permissions by company IP.
* Create a Policy to restrict resource deletion permissions by time.

**Module 9 — Clean up resources**

* Terminate unused EC2 Instances.
* Delete unnecessary EBS Volumes.
* Delete Snapshots if no longer used.
* Delete the AMIs created in the lab.
* Delete Security Groups, VPCs, or supplementary resources if no longer needed.
* Check the Billing Dashboard after cleanup.

---

#### Challenges and Solutions

**Challenges:**

* Initially, it is easy to confuse VPCs, Subnets, and Security Groups.
* When connecting to a Windows Instance, you need to decrypt the password using the exact key pair.
* When SSH-ing into Linux, errors easily occur due to incorrect `.pem` file permissions.
* If the wrong port or wrong source IP is opened in the Security Group, connection is impossible.
* Certain resources like EBS Snapshots, AMIs, and Elastic IPs may incur charges if you forget to delete them.
* When deploying the app, you may encounter package, firewall, port, or database connection errors.

**Solutions:**

* Double-check the network architecture before launching an instance.
* Name resources clearly following a format:
* `fcj-linux-vpc`
* `fcj-windows-vpc`
* `fcj-linux-sg`
* `fcj-windows-sg`


* Only open SSH/RDP to your personal IP; avoid opening it to the entire Internet.
* For Linux, run the key pair permission command before SSH:

```bash
chmod 400 first-kp.pem

```
