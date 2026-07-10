---
title: "Week 3 Worklog"
date: 2026-05-11
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
| Monday | - Read the Introduction to Amazon EC2 section <br> - Understand the workshop overview and hands-on architecture <br> - Prepare AWS account, Region, Key Pair, and connection environment | 11/05/2026 | 11/05/2026 | https://000004.awsstudygroup.com/ |
| Tuesday | - Module 2.1: Create a Linux VPC <br> - Module 2.2: Create VPC for Windows Instance <br> - Module 2.3: Create Security Group for Linux Instance <br> - Module 2.4: Create Security Group for Windows Instance | 12/05/2026 | 12/05/2026 | https://000004.awsstudygroup.com/ |
| Wednesday | - Module 3.1: Launch Microsoft Windows Server 2022 Instance <br> - Module 3.2: Connect from Computer to Windows Instance <br> - Verify Remote Desktop and Windows key pair login | 13/05/2026 | 13/05/2026 | https://000004.awsstudygroup.com/ |
| Thursday | - Module 4.1: Launch Amazon Linux Instance <br> - Module 4.2: Connect to Amazon Linux Instance <br> - Practice SSH connection to Linux Instance using key pair | 14/05/2026 | 14/05/2026 | https://000004.awsstudygroup.com/ |
| Friday | - Module 5.1: Modify EC2 Instance Type <br> - Module 5.2: Create and Manage EBS Snapshots <br> - Module 5.3: Create Custom AMI <br> - Module 5.4: Launch Instance from Custom AMI <br> - Module 5.5 - 5.7: Recover access and Remote Desktop to Ubuntu | 15/05/2026 | 15/05/2026 | https://000004.awsstudygroup.com/ |
| Saturday | - Module 6: Deploy AWS User Management Application on Amazon Linux <br> - Install LAMP Server, configure database, phpMyAdmin, Node.js, and deploy the application | 16/05/2026 | 16/05/2026 | https://000004.awsstudygroup.com/ |
| Sunday | - Module 7: Deploy Node.js Application on Windows EC2 <br> - Module 8: Cost & Usage Governance with IAM <br> - Module 9: Clean up resources | 17/05/2026 | 17/05/2026 | https://000004.awsstudygroup.com/ |

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

**Networking Preparation**

- Created Linux VPC and Windows VPC.
- Configured Security Groups for:
  - Linux EC2 (SSH, HTTP)
  - Windows EC2 (RDP)
-  _Evidence: Successfully configured VPCs and Security Groups._

**Windows EC2 Deployment**

- Launched Microsoft Windows Server 2022 EC2 Instance.
- Configured key pair authentication.
- Connected successfully using Remote Desktop.
-  _Evidence: Windows EC2 instance in running state._
-  _Evidence: Successful RDP connection._

**Amazon Linux EC2 Deployment**

- Launched Amazon Linux EC2 Instance.
- Connected successfully through SSH terminal.
- Configured Linux environment for deployment.
-  _Evidence: Amazon Linux EC2 running successfully._
-  _Evidence: SSH connection established._

**EC2 Basic Operations**

- Changed EC2 Instance Type.
- Created EBS Snapshots.
- Created Custom AMI.
- Launched EC2 from Custom AMI.
-  _Evidence: Snapshot successfully created._
-  _Evidence: Custom AMI available._
-  _Evidence: EC2 launched from AMI._

**Deploy Application on Amazon Linux**

- Installed Apache and PHP.
- Configured database services.
- Installed phpMyAdmin.
- Installed Node.js runtime.
- Deployed AWS User Management Application.
-  _Evidence: Apache test page working._
-  _Evidence: phpMyAdmin accessible._
-  _Evidence: Application running successfully._

**Deploy Application on Windows EC2**

- Installed XAMPP.
- Installed Node.js environment.
- Deployed sample Node.js application.
- Tested application through browser.
-  _Evidence: Node.js application running on Windows Server._

**IAM Governance Practice**

- Created IAM Policies to restrict EC2 usage.
- Restricted actions based on Region and Instance Type.
- Tested governance restrictions successfully.
-  _Evidence: IAM Policy restrictions functioning correctly._

**Resource Cleanup**

- Terminated EC2 Instances after lab completion.
- Deleted Snapshots and unused AMIs.
- Removed unnecessary Security Groups and networking resources.
- Checked Billing Dashboard to verify no abnormal charges.
-  _Evidence: All resources cleaned up successfully._

---

#### Difficulties and Solutions

**Difficulties:**

- Initially confused between networking components such as VPC and Security Groups.
- Remote connection failed when inbound rules were configured incorrectly.
- SSH authentication failed due to incorrect `.pem` file permissions.
- Some AWS resources continued generating charges if not cleaned up.
- Deployment issues occurred because of firewall and database configuration errors.

**Solutions:**

- Reviewed AWS networking concepts before deployment.
- Restricted SSH and RDP access to personal IP only.
- Fixed SSH permission issue using:

```bash
chmod 400 first-kp.pem