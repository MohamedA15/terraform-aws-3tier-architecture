Terraform 3-Tier AWS Architecture 

This project provisions a highly available 3-tier web architecture on Amazon Web Services (AWS) using Terraform. It is designed for learning Infrastructure as Code (IaC) concepts and automating the deployment of scalable applications in the cloud.

🏗️ Architecture Overview
The infrastructure consists of:

Networking (VPC + Subnets)
1 VPC (10.0.0.0/16)
2 Public Subnets (for load balancers, NAT)
4 Private Subnets (for application and database tiers)
Internet Gateway + NAT Gateway

Application Layer
Auto Scaling Groups for EC2 instances (Public + Private)
Application Load Balancers (ALB) for routing traffic
EC2 Launch Templates with Apache HTTP server (serves simple web page)

Database Layer
Amazon RDS (MySQL 8.0, db.t3.micro)
Deployed in private subnets
Security group restricts MySQL access to the VPC

Security
Security Groups for web + database
Public inbound: HTTP (80), SSH (22)
Private inbound: MySQL (3306) from VPC only
Egress: unrestricted

🌐 High-Level Diagram
                  Internet
                     │
              ┌──────▼──────┐
              │   Public    │
              │    ALB      │
              └──────┬──────┘
                     │
         ┌───────────┴───────────┐
         │                       │
   Public Subnet A         Public Subnet B
   (EC2 via ASG)           (EC2 via ASG)
         │                       │
 ┌───────▼───────┐       ┌───────▼───────┐
 │   Private ALB │       │   Private ALB │
 └───────┬───────┘       └───────┬───────┘
         │                       │
   Private Subnet A        Private Subnet B
   (App EC2 via ASG)       (App EC2 via ASG)
         │                       │
         └──────────────┬────────┘
                        │
                   Amazon RDS
                (Private Subnets)

⚙️ Deployment
Prerequisites
Terraform installed
AWS CLI installed and configured (aws configure)
An AWS account with IAM permissions

Steps
# Initialize Terraform
terraform init

# Preview resources to be created
terraform plan

# Apply changes (provision resources)
terraform apply -auto-approve

# Destroy resources when finished
terraform destroy -auto-approve

💸 Cost Considerations
Running this project will incur costs on AWS:
EC2 (t2.micro)
RDS (db.t3.micro)
NAT Gateway (≈ $0.045/hour + data transfer)
Elastic Load Balancers

👉 To avoid unexpected bills, destroy resources when not in use:
terraform destroy -auto-approve

🔐 Security Notes
Access keys are not stored in this repo (use environment variables or AWS CLI config).
Private subnets are not internet-facing.
Database is not public — only accessible inside the VPC.

🚀 Future Improvements
Add HTTPS support with ACM certificates
Implement Route53 DNS for custom domain
Add CloudWatch monitoring and logging
Add Terraform modules for better code structure