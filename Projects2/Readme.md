# Cloud Network Isolation with AWS VPC & EC2 Security

This project demonstrates how to design and implement a secure cloud network architecture using AWS services such as VPC, EC2, NAT Gateway, Security Groups, NACLs, IAM Roles, and Subnet isolation.

---

## Objective

Design and deploy a secure, isolated network in AWS using a custom VPC. Ensure private EC2 instances can connect to the internet securely via NAT, while maintaining strict control over access and communication.

---

## Implementation Steps

### 1. VPC Creation
- Created a custom VPC with CIDR block: `10.0.0.0/16`
- Enabled DNS resolution and hostnames

### 2. Subnet Design
- **Public Subnets**: `10.0.1.0/24` and `10.0.2.0/24`
- **Private Subnets**: `10.0.3.0/24` and `10.0.4.0/24`
- Distributed across two Availability Zones for high availability

### 3. Internet Gateway
- Created and attached an IGW to the VPC
- Updated Public Subnet route table to route `0.0.0.0/0` via IGW

### 4. NAT Gateway Setup
- Launched NAT Gateways in each Public Subnet using Elastic IPs
- Updated Private Subnet route tables to use NAT for outbound traffic

### 5. EC2 Deployment
- **Bastion Host** in Public Subnet for SSH jump access
- **Application Servers** in Private Subnets (no public IP)
- Installed required packages (Apache, Nginx, Node.js, etc.)

### 6. Security Groups
- Bastion SG: SSH allowed only from my IP
- App Server SG: Allows SSH only from Bastion SG, plus app ports (e.g., 80, 443)

### 7. NACL Configuration
- Created stateless rules to control subnet-level access
- Allowed essential ports (22, 80, 443), denied others for added protection

### 8. IAM Roles & Access Control
- Attached IAM roles to EC2s for secure access to S3, CloudWatch
- Followed least-privilege principle with managed policies

### 9. SSH Key Pair
- Created and used `.pem` key for SSH access to Bastion Host
- Connected to private instances through SSH tunneling

### 10. Connectivity Testing
- Verified:
  - SSH via Bastion → Private EC2
  - Internet access from private subnet via NAT
  - No public exposure of internal EC2s
  - Inter-subnet communication
- Enabled VPC Flow Logs for monitoring

---

## Outcome

- Achieved **secure network isolation** within AWS
- Private workloads can access the internet **without being exposed**
- Enforced **multi-layer security** using SGs, NACLs, and IAM
- **Highly available design** across multiple AZs

---


##  Technologies Used
- AWS VPC, Subnets, NAT Gateway, IGW
- Amazon EC2, IAM Roles
- Security Groups, NACLs
- AWS CLI / Console

---


##  Author

**Mohammed Ismail**

---



