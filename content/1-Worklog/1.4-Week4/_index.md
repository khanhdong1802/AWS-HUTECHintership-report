---
title: "Worklog Week 4"
date: 2026-05-18
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Objectives for Week 4:

- Learn the overview of Amazon RDS and its role in cloud-based relational database systems.
- Understand key features of RDS such as managed service, automated backup, scaling, Multi-AZ, and Read Replica.
- Practice creating VPC, Subnet, Security Group for EC2 and RDS.
- Create a DB Subnet Group to prepare the database deployment environment.
- Launch an EC2 Instance as an application server that connects to RDS.
- Create an RDS Database Instance and check its Endpoint, Port, and Username.
- Deploy the AWS FCJ Management application and connect it to the RDS database.
- Practice backup, snapshot, and database restore.
- Clean up resources after completing the lab to avoid unnecessary costs.

---

### Tasks to be completed this week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | --- | --- | --- | --- |
| Monday | - Read the Introduction section about Amazon RDS <br> - Understand what RDS, OLTP, DB Instance, and Endpoint are <br> - Learn about supported database engines such as MySQL, PostgreSQL, MariaDB, SQL Server, Oracle, and Aurora | 18/05/2026 | 18/05/2026 | https://000005.awsstudygroup.com/ |
| Tuesday | - Module 2.1: Create a VPC <br> - Create VPC, Public Subnet, and Private Subnet <br> - Configure multiple Availability Zones to prepare for RDS | 19/05/2026 | 19/05/2026 | https://000005.awsstudygroup.com/ |
| Wednesday | - Module 2.2: Create EC2 Security Group <br> - Module 2.3: Create RDS Security Group <br> - Open necessary ports for EC2 and restrict RDS access to only the EC2 Security Group | 20/05/2026 | 20/05/2026 | https://000005.awsstudygroup.com/ |
| Thursday | - Module 2.4: Create DB Subnet Group <br> - Module 3: Create EC2 Instance <br> - Launch Amazon Linux EC2, attach a Key Pair, and connect via SSH using MobaXterm | 21/05/2026 | 21/05/2026 | https://000005.awsstudygroup.com/ |
| Friday | - Module 4: Create RDS Database Instance <br> - Create a database instance, configure engine, username, password, VPC, and Security Group <br> - Check RDS status, Endpoint, and Port | 22/05/2026 | 22/05/2026 | https://000005.awsstudygroup.com/ |
| Saturday | - Module 5: Application Deployment <br> - Clone the AWS FCJ Management source code <br> - Install Node.js, npm packages, and MySQL client <br> - Create database, user table, and run the application on port 5000 | 23/05/2026 | 23/05/2026 | https://000005.awsstudygroup.com/ |
| Sunday | - Module 6: Backup and Restore <br> - Check backup, snapshot, and restore RDS <br> - Module 7: Clean up resources <br> - Delete EC2, RDS, Snapshot, Subnet Group, Security Group, and VPC to avoid unexpected costs | 24/05/2026 | 24/05/2026 | https://000005.awsstudygroup.com/ |

---

### Results Achieved in Week 4:

#### Knowledge

**What is Amazon RDS?**

- Understand that Amazon RDS is a relational database service managed by AWS.
- RDS allows users to create and operate databases without manually managing physical servers or database virtual machines.
- RDS is suitable for transaction processing systems such as user management, orders, payments, or structured data applications.
- RDS supports many database engines such as Amazon Aurora, MySQL, MariaDB, Oracle, SQL Server, and PostgreSQL.

**Managed Service in RDS**

- Understand that RDS is a managed service, so users do not need to directly manage the underlying operating system of the DB Instance.
- AWS supports tasks such as backup, patching, software updates, scaling, Multi-AZ, and failover.
- Users can focus more on database design, connection security, and application operation.

**VPC, Subnet, and DB Subnet Group**

- Understand that VPC is a virtual private network used to isolate AWS resources.
- Know how to create a VPC with public and private subnets.
- Public subnets are suitable for EC2 instances that need Internet access.
- Private subnets are suitable for RDS to limit direct access from the Internet.
- Understand that a DB Subnet Group is a collection of subnets assigned to RDS.
- A DB Subnet Group should include subnets in at least two Availability Zones to support high availability.

**Security Groups for EC2 and RDS**

- Understand that Security Group works as a firewall for EC2 and RDS.
- EC2 Security Group needs to open necessary ports such as:
  - SSH: `22`
  - HTTP: `80`
  - HTTPS: `443`
  - Application port: `5000`
- RDS Security Group needs to open the database port, for example MySQL/Aurora uses port `3306`.
- RDS should only allow connections from the EC2 Security Group instead of allowing wide public access.

**EC2 Instance as Application Server**

- Know how to create an Amazon Linux EC2 Instance.
- Know how to select Amazon Linux 2023 AMI.
- Know how to choose a suitable Instance Type such as `t2.micro` or `t3.micro`.
- Know how to attach a Key Pair for SSH access.
- Know how to connect to EC2 using MobaXterm through Public IP or Public DNS.

**RDS Database Instance**

- Know how to create an RDS Database Instance using Standard create.
- Know how to configure engine, version, template, master username, password, VPC, and Security Group.
- Know how to check the RDS status from `Creating` to `Available`.
- Know how to get the Endpoint, Port, and Username so that the application or EC2 can connect to the database.

**Deploying an Application Connected to RDS**

- Know how to clone source code from GitHub to EC2.
- Know how to install Node.js, npm, and required packages such as Express, dotenv, and MySQL.
- Know how to create a `.env` file to store database connection information, including Endpoint, Database Name, Username, and Password.
- Know how to create the `first_cloud_users` database, create the `user` table, and insert sample data.
- Know how to run the application using `npm start` and access it through port `5000`.

**Backup and Restore in RDS**

- Understand that RDS supports automated backup and manual snapshot.
- Know how to check backups in the Maintenance & backups tab.
- Know how to create a snapshot and restore it into a new DB Instance.
- Understand that a restored database will have a new Endpoint, so the application connection string must be updated if the new database is used.

**Resource Cleanup**

- Know how to delete RDS Database Instance, DB Snapshot, and DB Subnet Group.
- Know how to terminate EC2 Instance after completing the lab.
- Know how to delete Security Group, NAT Gateway, Elastic IP, and VPC if they are no longer used.
- Understand the importance of checking Billing Dashboard or Cost Explorer after cleanup to avoid unexpected charges.

---

#### Practice

**Module 1 — Introduction**

- Read the overview of Amazon RDS.
- Learn that RDS is a managed relational database service.
- Distinguish RDS from a database manually installed on EC2.
- Learn the following concepts:
  - DB Instance
  - Endpoint
  - Database Engine
  - Maintenance Window
  - Backup
  - Snapshot
  - Multi-AZ
  - Read Replica
-  _Evidence image: Amazon RDS workshop Introduction page._

**Module 2.1 — Create a VPC**

- Create a VPC for the RDS deployment environment.
- Configure IPv4 CIDR block.
- Create a public subnet for EC2.
- Create a private subnet for RDS.
- Select at least two Availability Zones to improve availability.
- Check auto-assign public IPv4 for the appropriate subnet.
-  _Evidence image: VPC created successfully._
-  _Evidence image: Public subnet and private subnet._

**Module 2.2 — Create EC2 Security Group**

- Create a Security Group for the EC2 Instance.
- Configure inbound rules:
  - SSH port `22`
  - HTTP port `80`
  - HTTPS port `443`
  - Custom TCP port `5000`
- Restrict SSH access to personal IP for better security.
- Record the Security Group ID for EC2 creation and RDS configuration.
-  _Evidence image: EC2 Security Group created._
-  _Evidence image: Inbound rules of EC2 Security Group._

**Module 2.3 — Create RDS Security Group**

- Create a separate Security Group for RDS.
- Configure inbound rule for MySQL/Aurora port `3306`.
- Set the source as EC2 Security Group instead of opening access to the public Internet.
- Check that the RDS Security Group is in the correct VPC.
-  _Evidence image: RDS Security Group created._
-  _Evidence image: Rule allowing EC2 to connect to RDS._

**Module 2.4 — Create DB Subnet Group**

- Go to Amazon RDS Console.
- Create a DB Subnet Group.
- Select the VPC created in the previous step.
- Select subnets in at least two Availability Zones.
- Prioritize private subnets for the database.
- Check the DB Subnet Group after it is created successfully.
-  _Evidence image: DB Subnet Group created._
-  _Evidence image: Subnets added to DB Subnet Group._

**Module 3 — Create EC2 Instance**

- Create an Amazon Linux EC2 Instance.
- Select Amazon Linux 2023 AMI.
- Choose a suitable Instance Type for the lab.
- Attach a Key Pair for SSH login.
- Attach the EC2 Security Group created earlier.
- Launch the instance and wait until its status becomes `running`.
- Connect to EC2 using MobaXterm.
- Check the terminal after successful SSH connection.
-  _Evidence image: EC2 Instance in running state._
-  _Evidence image: SSH connection to EC2 successful._

**Module 4 — Create RDS Database Instance**

- Go to Amazon RDS Console.
- Select Create database.
- Select Standard create.
- Select the database engine suitable for the workshop.
- Configure DB Instance identifier.
- Set master username and master password.
- Select VPC, DB Subnet Group, and RDS Security Group.
- Create the database instance.
- Wait until the RDS status changes to `Available`.
- Check Endpoint, Port, and Username.
-  _Evidence image: RDS Database Instance being created._
-  _Evidence image: RDS status is Available._
-  _Evidence image: RDS Endpoint and Port._

**Module 5 — Application Deployment**

- SSH into the EC2 Instance.
- Install Git on Amazon Linux.
- Clone the AWS FCJ Management repository.
- Install Node.js and npm.
- Install required packages for the application.
- Install MySQL client to connect to RDS.
- Create a `.env` file containing:
  - `DB_HOST`
  - `DB_NAME`
  - `DB_USER`
  - `DB_PASS`
- Connect to RDS using the Endpoint.
- Create the `first_cloud_users` database.
- Create the `user` table.
- Insert sample data into the table.
- Run the application with the following command:

```bash
npm start
```

- Access the application in the browser:

```bash
http://<EC2-Public-IP>:5000
```

- Check that the application displays data from RDS.
-  _Evidence image: Source code cloned successfully._
-  _Evidence image: Node.js and npm installed._
-  _Evidence image: MySQL connection to RDS successful._
-  _Evidence image: Application running on port 5000._

**Module 6 — Backup and Restore**

- Open RDS Console.
- Check the Monitoring tab to view database performance metrics.
- Check the Maintenance & backups tab.
- View automated backup information.
- Create or check a manual snapshot.
- Restore the snapshot into a new DB Instance.
- Wait until the restored DB Instance status becomes `Available`.
- Check the new Endpoint of the restored database.
- Check the connection and data in the restored database.
-  _Evidence image: RDS Monitoring._
-  _Evidence image: Backup information._
-  _Evidence image: Snapshot._
-  _Evidence image: DB Instance restored successfully._

**Module 7 — Clean up resources**

- Terminate unused EC2 Instance.
- Delete RDS Database Instance.
- Delete DB Snapshot if it is no longer needed.
- Delete DB Subnet Group.
- Delete EC2 Security Group and RDS Security Group.
- Delete NAT Gateway if created.
- Release Elastic IP if used.
- Delete VPC and related network resources.
- Check Billing Dashboard or Cost Explorer.
-  _Evidence image: EC2 Instance terminated._
-  _Evidence image: RDS Database Instance deleted._
-  _Evidence image: No remaining resources causing charges._

---

#### Difficulties and Solutions

**Difficulties:**

- It was initially easy to confuse public subnets and private subnets.
- Incorrect Security Group configuration could prevent EC2 from connecting to RDS.
- Opening RDS access too widely to the Internet is unsafe.
- When creating a DB Subnet Group, subnets must be selected from at least two Availability Zones.
- When creating RDS, the master password must be remembered because it cannot be viewed again directly.
- If the application cannot connect to the database, the issue may come from an incorrect Endpoint, port, username/password, or Security Group configuration.
- When restoring a snapshot, the new database will have a new Endpoint, so the application configuration file must be updated.
- RDS, Snapshot, NAT Gateway, or Elastic IP may generate costs if they are not deleted.

**Solutions:**

- Use clear resource names such as:
  - `fcj-rds-vpc`
  - `fcj-public-subnet`
  - `fcj-private-subnet`
  - `fcj-ec2-sg`
  - `fcj-rds-sg`
  - `fcj-rds-subnet-group`
  - `fcj-rds-db`
- Place EC2 in a public subnet so that it can be accessed via SSH.
- Place RDS in a private subnet to limit direct access from the Internet.
- EC2 Security Group should only allow SSH from personal IP.
- RDS Security Group should only allow source from the EC2 Security Group.
- Always check RDS Endpoint, Port, and `Available` status before connecting.
- Check the `.env` file when the application cannot connect to the database.
- After restoring a snapshot, update `DB_HOST` if using the new database.
- After the lab, review all resources in EC2, RDS, VPC, and Billing.

---

### Summary of Week 4:

In Week 4, I learned and practiced deploying an application system that uses **Amazon RDS** as a relational database on AWS. Through the workshop, I understood the role of RDS in simplifying database administration, including database instance creation, security configuration, backup, restore, and monitoring.

In addition, I practiced combining multiple AWS services such as VPC, Subnet, Security Group, EC2, and RDS to build a complete application model. EC2 acts as the application server, while RDS acts as the database server. Separating the application layer and the database layer makes the system easier to manage, more secure, and closer to real-world deployment models.

Through this week, I also realized that network and security configuration are very important when working with RDS. If Security Group, Subnet, or DB Subnet Group is configured incorrectly, the application will not be able to connect to the database. Therefore, before deploying the application, it is necessary to carefully check the network architecture, access permissions, and connection information.
