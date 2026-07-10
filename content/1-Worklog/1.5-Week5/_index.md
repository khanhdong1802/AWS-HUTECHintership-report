---
title: "Worklog Week 5"
date: 2026-05-25
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

- Understand the role of Amazon EC2 Auto Scaling in building scalable and highly available cloud applications.
- Learn how Auto Scaling Groups automatically adjust the number of EC2 instances based on workload demand.
- Understand how Application Load Balancer distributes incoming traffic across multiple application servers.
- Review the basic deployment architecture of the FCJ Management application on AWS.
- Prepare the required infrastructure including VPC, Subnets, Security Groups, EC2, and RDS.
- Create an AMI from a configured EC2 instance and use it to build a Launch Template.
- Create a Target Group and Application Load Balancer for the application tier.
- Create an Auto Scaling Group and attach it to the Load Balancer.
- Test manual scaling, scheduled scaling, dynamic scaling, and predictive scaling solutions.
- Clean up all created resources after completing the lab to avoid unexpected AWS costs.

---

### Tasks to be completed this week:

| Day | Tasks | Start Date | Completion Date | Reference |
| --- | --- | --- | --- | --- |
| Monday | - Read the introduction of the workshop: Deploying FCJ Management Application with Auto Scaling Group <br> - Understand the purpose of deploying a scalable cloud application <br> - Review the overall architecture using EC2, RDS, Load Balancer, and Auto Scaling Group <br> - Create the VPC `AutoScaling-Lab` <br> - Configure public subnets, private subnets, route tables, and multiple Availability Zones <br> - Create Security Groups for the application server and database | 25/05/2026 | 25/05/2026 | https://000006.awsstudygroup.com/ |
| Tuesday | - Launch the EC2 Instance `FCJ-Management` using Amazon Linux 2023 <br> - Attach Key Pair, Public Subnet, and Application Security Group <br> - Create a DB Subnet Group for Amazon RDS <br> - Create an RDS MySQL Database Instance <br> - Connect the EC2 Instance to RDS and prepare the initial database environment | 26/05/2026 | 26/05/2026 | https://000006.awsstudygroup.com/ |
| Wednesday | - Install Git, MariaDB client, Node.js, and NPM on EC2 <br> - Clone the FCJ Management source code from GitHub <br> - Configure the `.env` file to connect the application to RDS <br> - Install PM2 and run the Node.js application in the background <br> - Prepare custom metrics for Predictive Scaling and upload them to CloudWatch | 27/05/2026 | 27/05/2026 | https://000006.awsstudygroup.com/ |
| Thursday | - Create an AMI from the configured EC2 Instance <br> - Create the Launch Template `FCJ-Management-template` from the AMI <br> - Configure Instance Type, Key Pair, Subnet, and Security Group in the Launch Template <br> - Create the Target Group `FCJ-Management-TG` using HTTP protocol and port `5000` <br> - Configure health check settings for the Target Group | 28/05/2026 | 28/05/2026 | https://000006.awsstudygroup.com/ |
| Friday | - Create the Application Load Balancer `FCJ-Management-LB` <br> - Configure it as Internet-facing with IPv4 <br> - Attach public subnets from multiple Availability Zones <br> - Connect the Load Balancer to the Target Group <br> - Test the application through the Load Balancer DNS name and verify CRUD functions | 29/05/2026 | 29/05/2026 | https://000006.awsstudygroup.com/ |
| Saturday | - Create the Auto Scaling Group `FCJ-Management-ASG` from the Launch Template <br> - Configure Desired Capacity, Minimum Capacity, and Maximum Capacity <br> - Attach the Auto Scaling Group to the Load Balancer Target Group <br> - Enable Elastic Load Balancing Health Checks and CloudWatch group metrics <br> - Test Manual Scaling and Scheduled Scaling | 30/05/2026 | 30/05/2026 | https://000006.awsstudygroup.com/ |
| Sunday | - Configure Dynamic Scaling Policy based on ALB Request Count Per Target <br> - Create a Predictive Scaling Policy using CloudWatch custom metrics <br> - Analyze Load, Capacity, and Scaling charts in CloudWatch <br> - Clean up resources including ASG, Load Balancer, Target Group, Launch Template, AMI, EC2, RDS, DB Subnet Group, Security Groups, and VPC <br> - Check AWS Billing Dashboard to avoid unexpected costs | 31/05/2026 | 31/05/2026 | https://000006.awsstudygroup.com/ |

---

### Results achieved in week 5:

#### Knowledge

**Amazon EC2 Auto Scaling**

- Understood that Amazon EC2 Auto Scaling helps automatically adjust the number of EC2 instances according to application demand.
- Learned that Auto Scaling improves application availability by replacing unhealthy instances and launching new ones when needed.
- Understood that Auto Scaling can help optimize cost by increasing capacity during high traffic and reducing capacity during low traffic.
- Learned the basic components of an Auto Scaling architecture:
  - Launch Template
  - Auto Scaling Group
  - Minimum Capacity
  - Desired Capacity
  - Maximum Capacity
  - Scaling Policy
  - Health Check

**Application Load Balancer**

- Understood that Application Load Balancer distributes HTTP or HTTPS traffic across multiple EC2 instances.
- Learned that the Load Balancer helps prevent a single instance from becoming overloaded.
- Understood that Load Balancer improves fault tolerance by routing traffic only to healthy targets.
- Learned the relationship between Load Balancer, Listener, Target Group, and EC2 instances.
- Understood how the Load Balancer DNS name can be used to access the deployed application.

**Target Group**

- Understood that a Target Group is used to register EC2 instances that receive traffic from the Load Balancer.
- Learned how to configure the Target Group protocol and port for the FCJ Management application.
- Understood that the health check path and port are important for determining whether an instance is healthy.
- Learned that Auto Scaling Group can automatically register and deregister EC2 instances with the Target Group.

**Launch Template**

- Understood that a Launch Template stores configuration information used to launch EC2 instances automatically.
- Learned that a Launch Template may include AMI ID, Instance Type, Key Pair, Security Group, and other EC2 configuration settings.
- Understood that Auto Scaling Group uses the Launch Template to create new EC2 instances with the same environment.
- Learned that creating an AMI from a pre-configured EC2 instance helps simplify the deployment of identical application servers.

**Amazon Machine Image**

- Understood that an AMI is a snapshot-like image used to create EC2 instances with pre-installed software and configuration.
- Learned that the configured FCJ Management EC2 instance can be converted into an AMI.
- Understood that this AMI can be reused by the Launch Template to automatically create new application servers.

**Scaling Solutions**

- Understood the differences between multiple scaling methods:
  - Manual Scaling
  - Scheduled Scaling
  - Dynamic Scaling
  - Predictive Scaling
- Manual Scaling is used when the administrator directly changes the desired capacity.
- Scheduled Scaling is used when capacity changes are planned for a specific time.
- Dynamic Scaling is used when capacity changes depend on metrics such as request count or CPU utilization.
- Predictive Scaling uses historical metrics to forecast future traffic and prepare capacity in advance.

**CloudWatch Metrics**

- Understood that CloudWatch is used to collect and monitor AWS resource metrics.
- Learned how scaling policies depend on CloudWatch metrics to make scaling decisions.
- Understood that Predictive Scaling requires enough metric data to generate forecast charts.
- Learned how to read graphs related to load, capacity, and scaling behavior.

---

#### Practice

**Module 1 — Introduction**

- Read the workshop introduction.
- Understood the goal of deploying FCJ Management Application with Auto Scaling Group.
- Reviewed the high-level architecture using EC2, RDS, Application Load Balancer, and Auto Scaling Group.
- Learned why scalable architecture is needed when traffic changes over time.
- Understood that this workshop builds on the basic FCJ Management application deployment.
-  _Evidence image: Workshop introduction page._
-  _Evidence image: Overall architecture diagram._

**Module 2 — Preparation**

- Prepared the AWS environment for the lab.
- Created a VPC for the Auto Scaling workshop.
- Created public subnets for EC2 instances and Load Balancer.
- Created private subnets for RDS Database Instance.
- Configured route tables and subnet associations.
- Created Security Groups for the application server and RDS.
- Allowed required inbound traffic for SSH, HTTP, and application port.
- Limited RDS access to the application Security Group.
-  _Evidence image: VPC created successfully._
-  _Evidence image: Public and private subnets._
-  _Evidence image: Security Group inbound rules._

**Module 3 — Create Launch Template**

- Created an AMI from the configured EC2 Instance.
- Created the Launch Template for the FCJ Management application.
- Selected the created AMI as the base image.
- Configured Instance Type, Key Pair, and Security Group.
- Checked the Launch Template details after creation.
- Understood that the Auto Scaling Group will use this template to launch new EC2 instances.
-  _Evidence image: AMI created successfully._
-  _Evidence image: Launch Template configuration._

**Module 4 — Setting Up Load Balancer**

- Created a Target Group for the application servers.
- Selected the target type as EC2 instances.
- Configured HTTP protocol and application port `5000`.
- Configured the health check settings.
- Created an Application Load Balancer.
- Configured it as Internet-facing.
- Selected IPv4 as the address type.
- Attached public subnets from multiple Availability Zones.
- Connected the Load Balancer listener to the Target Group.
- Tested access to the FCJ Management application through the Load Balancer DNS name.
-  _Evidence image: Target Group created._
-  _Evidence image: Application Load Balancer created._
-  _Evidence image: Application accessed through Load Balancer._

**Module 5 — Test**

- Accessed the application through the Load Balancer DNS name.
- Tested the main functions of the FCJ Management application.
- Verified that Create, Read, Update, and Delete operations worked correctly.
- Confirmed that the application could connect to the RDS database.
- Checked whether the registered targets remained healthy.
-  _Evidence image: CRUD function tested successfully._

**Module 6 — Create Auto Scaling Group**

- Created an Auto Scaling Group from the Launch Template.
- Selected the VPC and subnets for the Auto Scaling Group.
- Attached the Auto Scaling Group to the existing Target Group.
- Configured Desired Capacity, Minimum Capacity, and Maximum Capacity.
- Enabled Elastic Load Balancing Health Checks.
- Enabled CloudWatch group metrics collection.
- Verified that EC2 instances were launched automatically by the Auto Scaling Group.
- Checked whether new instances were registered to the Target Group.
-  _Evidence image: Auto Scaling Group created._
-  _Evidence image: EC2 instances launched by Auto Scaling Group._

**Module 7 — Test Solutions**

- Tested Manual Scaling by changing the desired capacity.
- Tested Scheduled Scaling by creating a scheduled action.
- Tested Dynamic Scaling using a scaling policy based on Load Balancer metrics.
- Reviewed Predictive Scaling metrics in CloudWatch.
- Compared actual load with predicted load.
- Observed how capacity changed according to scaling policies.
-  _Evidence image: Manual Scaling result._
-  _Evidence image: Scheduled Scaling action._
-  _Evidence image: Dynamic Scaling policy._
-  _Evidence image: Predictive Scaling chart._

**Module 8 — Cleanup Resources**

- Deleted the Auto Scaling Group.
- Deleted the Application Load Balancer.
- Deleted the Target Group.
- Deleted the Launch Template.
- Deregistered or deleted the AMI and related snapshots if no longer needed.
- Terminated EC2 instances created during the lab.
- Deleted the RDS Database Instance.
- Deleted the DB Subnet Group.
- Deleted Security Groups that were no longer used.
- Deleted the VPC, subnets, route tables, and other networking resources if they were created only for the lab.
- Checked the AWS Billing Dashboard after cleanup.
-  _Evidence image: Resources cleaned up successfully._

---

### Difficulties encountered in week 5:

- The Auto Scaling architecture included many components, so it was necessary to understand the relationship between Launch Template, Target Group, Load Balancer, and Auto Scaling Group.
- It was easy to confuse the Security Group rules for EC2, Load Balancer, and RDS.
- The application could not be accessed if the Target Group port or health check configuration was incorrect.
- The Load Balancer DNS name needed some time before it became available.
- Auto Scaling Group required correct subnet and Target Group configuration to launch and register EC2 instances properly.
- Predictive Scaling was more difficult to understand because it depends on CloudWatch metric data and forecasting charts.
- Resource cleanup required careful checking because some resources could not be deleted while still attached to other services.

---

### Solutions and lessons learned:

- Checked each AWS component step by step instead of configuring everything at once.
- Verified Security Group inbound rules whenever the application could not be accessed.
- Checked Target Group health status to identify whether EC2 instances were ready to receive traffic.
- Used the Load Balancer DNS name instead of accessing a single EC2 Public IP.
- Checked Auto Scaling activity history to understand why an instance was launched or terminated.
- Used CloudWatch metrics to observe traffic, capacity, and scaling behavior.
- Cleaned resources in the correct order: Auto Scaling Group, Load Balancer, Target Group, Launch Template, AMI, EC2, RDS, Security Groups, and VPC.

---

### Evaluation of week 5:

- Completed the main steps of deploying the FCJ Management application with Auto Scaling Group.
- Understood how to build a scalable application architecture on AWS.
- Practiced creating Launch Template, Load Balancer, Target Group, and Auto Scaling Group.
- Tested different scaling methods including manual, scheduled, dynamic, and predictive scaling.
- Improved understanding of high availability, fault tolerance, and cost optimization in AWS.
- Learned the importance of monitoring and cleanup when working with cloud resources.

---

### Orientation for next week:

- Continue learning about advanced AWS deployment architectures.
- Review monitoring and logging services such as Amazon CloudWatch.
- Practice reading CloudWatch metrics and alarms in more detail.
- Learn how to improve application security using IAM roles and least privilege access.
- Study how to optimize cloud cost when using EC2, RDS, Load Balancer, and Auto Scaling.
- Prepare more screenshots and explanations for the final report.
