# AWS 3-Tier Scalable Web Application

This project implements a **secure, scalable 3-tier web application architecture on AWS**, with separate networking, compute, and database layers.

The infrastructure was built inside a custom VPC and integrates **EC2, Application Load Balancer, Auto Scaling, RDS MySQL, S3, IAM, and CloudWatch** to demonstrate real-world cloud infrastructure design.

## ☁️ AWS Services

* **Amazon VPC** – Custom network using `10.0.0.0/16`
* **Subnets** – Public and private subnet architecture across Availability Zones
* **Internet Gateway & Route Tables** – Internet and internal traffic routing
* **Security Groups** – Layer-based network access control
* **Amazon EC2** – Web server hosting
* **Apache** – Web server deployed on Amazon Linux
* **Application Load Balancer** – Distributes incoming HTTP traffic
* **Target Group** – EC2 registration and health checks
* **Auto Scaling Group** – Manages EC2 capacity
* **Launch Template** – Standardizes EC2 instance configuration
* **Amazon RDS** – MySQL database hosted in private subnets
* **Amazon S3** – Object storage
* **IAM** – Role-based access for EC2
* **Amazon CloudWatch** – Infrastructure monitoring

## 🏗️ Architecture

```text
                         INTERNET
                            |
                            v
                   Application Load Balancer
                            |
                            v
                    Auto Scaling Group
                       /          \
                      v            v
                   EC2 #1       EC2 #2
                  Apache        Apache
                      \            /
                       \          /
                        v        v
                         RDS MySQL
                       (Private)

                   EC2 --------> S3
                    |
                    v
                IAM Role
```

## 🔧 Implementation

### 1. Network Infrastructure

* Created a custom VPC with CIDR `10.0.0.0/16`
* Designed public and private subnets across Availability Zones
* Configured route tables for public and private traffic
* Attached an Internet Gateway for internet-facing resources
* Separated application and database networking

### 2. Security

Implemented separate Security Groups for each application layer:

```text
Internet
   |
   | HTTP :80
   v
ALB Security Group
   |
   | HTTP :80
   v
EC2 Security Group
   |
   | MySQL :3306
   v
RDS Security Group
```

This prevents the database from being directly exposed to the internet and restricts communication between tiers.

### 3. Web & Compute Layer

* Launched an Amazon Linux EC2 instance
* Installed and configured Apache
* Created a Launch Template containing the EC2 configuration
* Created an Auto Scaling Group using the Launch Template
* Configured the infrastructure to support multiple EC2 instances

### 4. Load Balancing

* Created an Application Load Balancer
* Configured an HTTP listener
* Created a Target Group for the EC2 instances
* Configured HTTP health checks on `/`
* Verified that the EC2 target was healthy

Traffic therefore follows:

```text
Client → ALB → Target Group → Healthy EC2
```

### 5. Database Layer

* Deployed MySQL using Amazon RDS
* Created an RDS subnet group using private subnets
* Configured RDS Security Group access for MySQL on port `3306`
* Tested connectivity between EC2 and RDS
* Created and queried a MySQL table from the EC2 environment

### 6. S3 & IAM Integration

Created an IAM role for EC2 and used it to access S3 without storing AWS access keys on the instance.

The integration was tested by uploading and listing objects from the S3 bucket:

```bash
aws s3 cp test.txt s3://bucket-name/
aws s3 ls s3://bucket-name/
```

### 7. Monitoring

Used Amazon CloudWatch to monitor EC2 performance metrics including CPU utilization and network activity.

## 🧪 Testing & Validation

The following components were tested during implementation:

* EC2 web server deployment
* ALB listener and routing
* Target Group health checks
* ALB → EC2 traffic
* EC2 → RDS MySQL connectivity
* EC2 → S3 access through IAM
* CloudWatch EC2 monitoring

The `screenshots` folder contains the AWS console screenshots documenting the implementation.
