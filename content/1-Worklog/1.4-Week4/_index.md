---
title: "Week 4 Worklog"
date: 2026-05-11
weight: 4
chapter: false
pre: "  1.4.  "

---
Week 4 Objectives:

* Explore the overall landscape of Amazon RDS and its vital role in cloud database architecture.
* Understand the core features of RDS, including: managed service, automated backups, scalability, Multi-AZ architecture, and Read Replicas.
* Setup a VPC network, Subnets, and configure Security Groups for both EC2 and RDS.
* Create a DB Subnet Group to establish a standard network environment for database deployment.
* Install and launch an EC2 virtual machine to act as an application server connecting directly to RDS.
* Provision an RDS Database Instance and gather crucial information such as Endpoint, Port, and Username.
* Deploy the AWS FCJ Management application to the environment and configure it to communicate with the RDS database.
* Familiarize with database operations like backup, manual snapshot creation, and restore.
* Clean up and delete all provisioned resources after completing the lab to optimize costs.

---

### Tasks to be Implemented This Week:

| Day | Task Category | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Research introductory documentation on Amazon RDS. <br> - Clarify concepts: RDS, OLTP systems, DB Instance, Endpoint. <br> - List supported database engines: MySQL, PostgreSQL, MariaDB, SQL Server, Oracle, and Aurora. | 11/05/2026 | 11/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Tuesday | - Module 2.1: Create a VPC. <br> - Setup a VPC network including both Public and Private Subnets. <br> - Deploy across multiple Availability Zones to ensure high availability for RDS. | 12/05/2026 | 12/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Wednesday | - Module 2.2: Create EC2 Security Group. <br> - Module 2.3: Create RDS Security Group. <br> - Establish firewall rules (open ports) for EC2 and configure RDS to only allow incoming traffic from the EC2 Security Group. | 13/05/2026 | 13/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Thursday | - Module 2.4: Create DB Subnet Group. <br> - Module 3: Create EC2 Instance. <br> - Provision an Amazon Linux virtual machine, attach a Key Pair, and establish a remote connection via SSH using MobaXterm. | 14/05/2026 | 14/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Friday | - Module 4: Create RDS Database Instance. <br> - Proceed to create the database, select the engine, set up account credentials, and attach the corresponding VPC and Security Group. <br> - Verify the Available status and retrieve connection details (Endpoint, Port). | 15/05/2026 | 15/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Saturday | - Module 5: Application Deployment. <br> - Download the AWS FCJ Management source code from the repository. <br> - Setup the Node.js environment, npm libraries, and MySQL client. <br> - Initialize the database, data tables (user), and run the application on port 5000. | 16/05/2026 | 16/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| Sunday | - Module 6: Backup and Restore. <br> - Test the backup, snapshot, and RDS restoration features. <br> - Module 7: Clean up resources. <br> - Delete all resources (EC2, RDS, Snapshots, Security Groups, VPC, etc.) to avoid incurring additional charges. | 17/05/2026 | 17/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
---

### Week 4 Achievements:

#### Knowledge

**What is Amazon RDS?**

* Grasped that it is a relational database service fully managed by the AWS ecosystem.
* Recognized the convenience of RDS as it allows operating a database without worrying about maintaining physical servers or operating systems.
* It is well-suited for systems with demanding transaction processing (orders, users, payments) or clearly structured data warehouses.
* Learned the list of compatible engines including: Amazon Aurora, Oracle, SQL Server, MySQL, PostgreSQL, and MariaDB.

**Managed Service in RDS**

* Understood the nature of a "managed service", freeing users from the burden of server OS administration.
* Routine maintenance tasks such as patching, software updates, backups, scaling, and failover are handled automatically by AWS.
* Saves time so the technical team can focus on optimizing data structures and application development.

**VPC, Subnets, and DB Subnet Group**

* Grasped the concept of a VPC as an isolated virtual network environment to protect AWS resources.
* Differentiated and learned how to deploy two types of subnets: public and private.
* Public subnets are allocated for EC2 instances to communicate with the Internet.
* Private subnets are used to host RDS, hiding the database from public network risks.
* Understood the definition of a DB Subnet Group as a cluster of subnets specifically designated for the RDS system.
* Comprehended the DB Subnet Group design principle: it must span at least 2 Availability Zones for fault tolerance.

**Security Groups for EC2 and RDS**

* Viewed Security Groups as virtual firewalls providing instance-level protection (for both EC2 and RDS).
* For EC2, required to open basic communication ports:
* `22` for SSH
* `80` for HTTP
* `443` for HTTPS
* `5000` for the custom application


* For RDS (e.g., using MySQL/Aurora), required to allow access via port `3306`.
* Applied the principle of least privilege: Do not expose the database port to the Internet; only allow inbound traffic originating from the EC2 instance's Security Group.

**EC2 Instance as an Application Server**

* Mastered the steps to launch an EC2 instance running Amazon Linux.
* Selected the correct image (Amazon Linux 2023 AMI).
* Configured a cost-effective instance type (`t2.micro` or `t3.micro`).
* Managed Key Pairs for secure authentication during connection.
* Used MobaXterm to SSH into the system via Public IP/DNS.

**RDS Database Instance**

* Proficiently provisioned an RDS Database Instance using the Standard create option.
* Learned how to customize parameters: engine type, version, login credentials (master user/pass), and assigned the VPC and Security Group.
* Recognized the state transition process of the database from `Creating` to `Available`.
* Knew how to extract the connection string (Endpoint) and port information to configure the app server.

**Deploying an Application Connected to RDS**

* Learned how to download source code from GitHub to the EC2 server environment.
* Completed the installation of Node.js, the npm package manager, and dependencies (Express, dotenv, MySQL).
* Declared a `.env` environment file to secure sensitive information: Endpoint, database name, username, and password.
* Connected to the newly created database, set up the `first_cloud_users` database, created the `user` table, and inserted test data.
* Started the web app via the `npm start` command and tested its functionality on the browser over port `5000`.

**Backup and Restore in RDS**

* Understood the difference between automated backups and manual snapshots.
* Monitored backups in the Maintenance & backups section on the console.
* Learned how to export a snapshot and use it to stand up a completely new DB Instance.
* Understood that after restoration, the system will assign a different Endpoint, requiring updates to the connection configuration on the application side.

**Resource Cleanup**

* Mastered the procedure to remove the RDS Database Instance, Snapshots, and related DB Subnet Groups.
* Terminated the EC2 virtual machine immediately after finishing the lab.
* Sequentially deleted network components such as Security Groups, NAT Gateways, Elastic IPs, and the VPC.
* Realized the importance of cross-checking the Cost Explorer or Billing Dashboard to ensure no unexpected charges occur due to leftover resources.

---

#### Practice

**Module 1 — Introduction**

* Researched the general introduction to the Amazon RDS solution.
* Clarified the nature of a managed relational database service.
* Compared the differences between using RDS and self-hosting a database on an EC2 instance.
* Familiarized with industry terminology:
* DB Instance
* Connection string (Endpoint)
* Database Engine
* Maintenance Window
* Backup & Snapshot
* Multi-AZ Architecture
* Read Replica


**Module 2.1 — Create a VPC**

* Established a virtual private network (VPC) for the RDS lab.
* Declared the IPv4 CIDR range.
* Configured a public subnet specifically for EC2.
* Configured a private subnet specifically for the RDS database.
* Selected 2 different Availability Zones to enhance fault tolerance.
* Enabled auto-assign public IPv4 for the appropriate subnet.


**Module 2.2 — Create EC2 Security Group**

* Built a Security Group acting as a network filter for the EC2 server.
* Added Inbound rules:
* Port `22` (Used for SSH)
* Port `80` (Used for HTTP)
* Port `443` (Used for HTTPS)
* Port TCP `5000` (For the custom application)


* Tightened security by only allowing SSH from the personal machine's IP address.
* Saved this Security Group ID for use in subsequent steps.


**Module 2.3 — Create RDS Security Group**

* Set up an independent network filter for RDS.
* Created an Inbound rule to allow port `3306` (for MySQL/Aurora).
* In the Source section, specified to only accept traffic from the previously created EC2 Security Group, strictly avoiding the "Anywhere" option.
* Verified that the RDS Security Group was placed into the correct lab VPC.

**Module 2.4 — Create DB Subnet Group**

* Logged into the Amazon RDS management console.
* Navigated to the DB Subnet Group creation section.
* Bound it to the newly created VPC.
* Selected subnets belonging to at least 2 distinct Availability Zones.
* Ensured only private subnets were added to enhance database security.
* Verified the information after the system reported successful creation.


**Module 3 — Create EC2 Instance**

* Proceeded to Launch a new Amazon Linux instance.
* Specified the operating system as Amazon Linux 2023 AMI.
* Configured basic hardware specifications suitable for learning needs.
* Specified a Key Pair for SSH access.
* Attached the previously prepared EC2 Security Group.
* Activated the instance and waited for the status to show `running`.
* Used MobaXterm to connect to the server.
* Checked the command prompt (terminal) to confirm successful login.


**Module 4 — Create RDS Database Instance**

* Returned to the Amazon RDS management dashboard.
* Clicked Create database.
* Enabled the Standard create option for full configuration freedom.
* Selected the corresponding database engine as instructed.
* Set an identifier name for the DB Instance.
* Provided master username and password information.
* Connected the instance to the corresponding VPC, DB Subnet Group, and RDS Security Group.
* Started the database creation process.
* Waited until the status transitioned from `Creating` to `Available`.
* Recorded the information: Endpoint, Port, and Username.


**Module 5 — Application Deployment**

* Established an SSH connection to the EC2 server.
* Installed the Git tool on the Amazon Linux platform.
* Cloned the AWS FCJ Management project to the virtual machine.
* Set up the Node.js execution environment and npm package manager.
* Installed all the necessary application dependencies.
* Installed the MySQL client package to communicate with the database via command line.
* Initialized the `.env` environment variable file with the parameters:
* `DB_HOST`
* `DB_NAME`
* `DB_USER`
* `DB_PASS`


* Used the Endpoint information to test the connection to RDS.
* Initialized a database named `first_cloud_users`.
* Built the `user` data table.
* Inserted a few sample records for testing.
* Started the web app using the command:

```bash
npm start
