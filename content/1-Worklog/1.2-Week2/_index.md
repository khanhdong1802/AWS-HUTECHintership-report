---
title: "Week 2 Worklog"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Objectives for Week 2:

- Understand the overview of Amazon VPC and core components in AWS network infrastructure (Subnet, Route Table, Internet Gateway, NAT Gateway).
- Set up a VPC network distributed across 2 Availability Zones (AZs) meeting Public and Private Subnet standards.
- Practice and compare 3 methods for secure authentication and connection to Private EC2 Instances: SSH via Bastion Host, EC2 Instance Connect (EIC) Endpoint, and AWS Systems Manager (SSM) Session Manager.
- Get familiar with the VPC Reachability Analyzer tool to inspect and debug network traffic routing logic.
- Enhance the Cost Optimization mindset by managing and cleaning up billable network resources after completing the lab.

---

### Tasks to be implemented this week:

| Day | Task | Start Date | End Date | Source Material |
| --- | --- | --- | --- | --- |
| Mon | - Prepare AWS account, select `us-east-1` Region <br> - Read overview documentation on Amazon VPC and AWS Site-to-Site VPN <br> - Prepare Key Pair and connection environment | 27/04/2026 | 27/04/2026 | https://000003.awsstudygroup.com/vi/ |
| Tue | - Set up core VPC infrastructure (CIDR `10.10.0.0/16`) <br> - Create 4 Subnets distributed across 2 Availability Zones (AZ 1a, 1b) <br> - Create and attach an Internet Gateway (IGW) to the VPC | 28/04/2026 | 28/04/2026 | https://000003.awsstudygroup.com/vi/ |
| Wed | - Deploy a NAT Gateway in Public Subnet 1 using Zonal mode <br> - Allocate and associate an Elastic IP to the NAT Gateway <br> - Configure Route Tables for Public and Private Subnets | 29/04/2026 | 29/04/2026 | https://000003.awsstudygroup.com/vi/ |
| Thu | - Launch Public and Private EC2 Instances (t3.micro, Amazon Linux 2023) <br> - Perform traditional connection using SSH Jump via Bastion Host <br> - Test Private Subnet network activity through the NAT Gateway using the internet ping command | 30/04/2026 | 30/04/2026 | https://000003.awsstudygroup.com/vi/ |
| Fri | - Use the VPC Reachability Analyzer tool to test network connectivity <br> - Analyze data packet routing logic between the Public EC2 and Private EC2 | 01/05/2026 | 01/05/2026 | https://000003.awsstudygroup.com/vi/ |
| Sat | - Create and configure an EC2 Instance Connect (EIC) Endpoint service in the Private Subnet <br> - Configure the Security Group and test direct connection to the Private EC2 without going through the public internet | 02/05/2026 | 02/05/2026 | https://000003.awsstudygroup.com/vi/ |
| Sun | - Configure IAM Role and set up 3 VPC Interface Endpoints to run SSM Session Manager <br> - Practice direct Shell management connection via web browser <br> - Perform resource clean up to optimize system costs | 03/05/2026 | 03/05/2026 | https://000003.awsstudygroup.com/vi/ |

---

### Achievements for Week 2:

#### Knowledge

**Amazon VPC and Core Network Infrastructure**
- Understand that a VPC is a virtual private network that completely isolates your resources within the AWS cloud computing infrastructure.
- Clearly distinguish the nature of a Public Subnet (attached to an Internet Gateway) and a Private Subnet (only for internal connections or outbound internet access via a NAT mechanism).
- Understand that a Route Table acts as a "signpost" system routing packets. The default route `10.10.0.0/16 -> local` ensures all internal traffic always routes safely through the AWS backbone infrastructure.

**Internet Gateway and NAT Gateway**
- The Internet Gateway (IGW) acts as a two-way portal (Inbound and Outbound) allowing the Public Subnet to connect directly to the public Internet.
- The NAT Gateway is a one-way portal (Outbound-only) that helps servers in the Private Subnet reach the Internet (for software updates, library downloads, API calls) while completely blocking any inbound traffic from the outside.
- Master the newly updated Availability mode configuration (Regional vs. Zonal) of the NAT Gateway and clearly understand the concept of High Availability (HA) in a real Production environment.

**VPC Reachability Analyzer**
- Know how to use the VPC Reachability Analyzer tool to analyze "logical" network connectivity between cloud resources without actually sending any packets.
- Helps isolate and quickly debug barriers caused by misconfigurations in Route Tables, Security Groups, or Network ACLs (NACL).

**3 Solutions for Connecting and Managing Private EC2**
- **SSH Jump via Bastion Host (Traditional):** Uses a Public EC2 server as a transit station to connect. This solution requires users to manually manage key pair files, open port 22 to the internet, and strictly adhere to file permissions (`chmod 400`).
- **EC2 Instance Connect (EIC) Endpoint (Modern):** A virtual endpoint managed directly by AWS within the VPC, helping establish an SSH session via the internal AWS backbone network. Completely free, requires no Public IP, NAT Gateway, or Bastion host, and has built-in audit logging via CloudTrail.
- **SSM Session Manager (Zero Trust):** Allows direct terminal access and management of EC2 right from the web browser via the SSM Agent mechanism and an IAM Role (`AmazonSSMManagedInstanceCore`). This method achieves maximum security by not requiring any inbound ports opened to the public Internet; however, it requires fixed investment costs for Interface Endpoints (`ssm`, `ssmmessages`, `ec2messages`).

**Cost Optimization**
- Understand the importance of the cost optimization mindset on the Cloud. Identify budget-draining background resources such as the NAT Gateway (~$32/month), Interface Endpoints, or unassociated hanging Elastic IPs to perform timely clean-up.

---

#### Hands-on Practice

**Module 1 — Build Core VPC Infrastructure**
- Set up a VPC network with the CIDR block `10.10.0.0/16` in the `us-east-1` (N. Virginia) region.
- Successfully allocated 4 Subnets evenly across 2 Availability Zones (1a, 1b) meeting Public and Private Subnet configurations.
- Created and directly attached an Internet Gateway (IGW) to the VPC.
- Successfully launched a NAT Gateway located in Public Subnet 1 in Zonal mode and associated it with an Elastic IP.
- Correctly configured `Route table-Public` to route default traffic `0.0.0.0/0` to the IGW and `Route table-Private` to route `0.0.0.0/0` to the NAT Gateway system.
-  _Proof Image: List of VPCs, Subnets, and successfully routed Route Tables._
-  _Proof Image: NAT Gateway in Available status accompanied by an Elastic IP._

**Module 2 — Verify Traditional Connection via Bastion Host**
- Successfully launched 2 EC2 Instances (`t3.micro`, Amazon Linux 2023) including a Public instance (`10.10.1.131`) and a Private instance (`10.10.3.142`) using the shared key pair `aws-keypair-v2`.
- Configured transit SSH from the personal machine through the Bastion Host to access the Private EC2 terminal. Executed the secure command `chmod 400` for the `.pem` key file.
- Ran the test command `ping -c 4 google.com` on the Private machine to confirm network packets passed through the NAT Gateway and received successful replies.
-  _Proof Image: Running status of the 2 EC2 Instances on the management console._
-  _Proof Image: Terminal successfully accessing the Private EC2 via Bastion Host and ping command results._

**Module 3 — Debug Network Connectivity with VPC Reachability Analyzer**
- Initiated a connectivity analysis to check the data flow from the Public EC2 machine to the Private EC2 machine.
- Checked the Security Group rules and received a system analysis result showing a `Reachable` status.
-  _Proof Image: Interface showing the Reachable result on the VPC Reachability Analyzer._

**Module 4 — Deploy Connection Solution using EC2 Instance Connect (EIC) Endpoint**
- Created an EIC Endpoint (`eice-0f7c...`) located in the Private Subnet network range.
- Updated the Inbound rule on the Private EC2's Security Group, only accepting port 22 access originating from the EIC Endpoint's own Security Group.
- Performed a direct SSH connection into the Private EC2's operating system from the AWS Console without going through the public internet or Bastion Host.
-  _Proof Image: EIC Endpoint information successfully created and in Available status._
-  _Proof Image: SSH terminal window connecting to the Private Instance via the EIC Endpoint solution._

**Module 5 — Set up Zero Trust Solution with SSM Session Manager**
- Created an IAM Role granting cloud management permissions containing the managed policy `AmazonSSMManagedInstanceCore` and directly attached it to both EC2 servers.
- Created 3 VPC Interface Endpoints (`ssm`, `ssmmessages`, `ec2messages`) using AWS PrivateLink located in the Private Subnet, enabled the DNS name option, and opened port 443 inbound for the entire `10.10.0.0/16` network range.
- Logged in to control the Private EC2 server via the Session Manager feature directly on the web browser interface without needing a key pair or opening the SSH port.
-  _Proof Image: Available status of the 3 VPC Interface Endpoints._
-  _Proof Image: Session Manager terminal interface running directly on the Web Browser._

**Module 6 — Clean up Resources**
- Deleted the NAT Gateway to stop hourly system billing.
- Completely Released the empty Elastic IP to prevent unassociated IP resource charges.
- Completely deleted the 3 SSM VPC Interface Endpoints set up for the lab.
- Kept the core VPC system and EIC Endpoint (completely free) to reuse for the upcoming weekly labs.
-  _Proof Image: NAT Gateway and VPC Endpoints resources completely cleared from the system._

---

#### Challenges and Solutions

**Challenges:**
- Easily confused about the operational nature and routing table configuration between the Internet Gateway (two-way) and the NAT Gateway (one-way).
- Encountered the SSH connection denied error `WARNING: UNPROTECTED PRIVATE KEY FILE!` when copying the `.pem` key pair file to the Bastion Host transit machine and forgetting to reconfigure file permissions.
- The SSM Agent system on the Private EC2 machine failed to register with the Systems Manager service because the "Enable DNS name" option was left unchecked when creating the Interface Endpoints.
- High risk of incurring large hidden billing charges from hourly billed resources like the NAT Gateway (~$32/month) and Interface Endpoints if accidentally left running/undeleted after completing the lab.

**Solutions:**
- Systematize knowledge by independently drawing a mind map of the traffic flow from the internet environment through each gateway and subnet to deeply grasp the infrastructure architecture.
- Strictly adhere to Linux system security standards, running the command `chmod 400 aws-keypair-v2.pem` immediately after uploading the key pair file to the server.
- Carefully read the lab instructions, deeply inspect the VPC's DNS resolution attributes, and accurately configure inbound port 443 for the endpoints' Security Groups.
- Establish a methodical resource cleanup checklist after every study session, and thoroughly check the Billing Dashboard periodically to detect and prevent unstopped resources early.
