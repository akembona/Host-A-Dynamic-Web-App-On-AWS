# Dynamic Website Deployment on AWS – DevOps Project

This repository contains the architecture diagram, deployment scripts, and database migration tools for hosting a dynamic website on AWS using a secure, scalable, and fault-tolerant infrastructure.

---

## 📌 Project Overview

The goal of this project was to design and deploy a highly available, secure, and scalable **dynamic website** using AWS cloud services, following DevOps best practices.  
The infrastructure is provisioned to ensure **high availability**, **fault tolerance**, and **scalability**, while maintaining **security** at every layer.

---

## 🏗️ Architecture Highlights

The infrastructure was built with the following AWS components:

1. **VPC Configuration**  
   - Created a Virtual Private Cloud (VPC) spanning **two Availability Zones**.  
   - Configured **public and private subnets** for separation of resources.

2. **Internet Access & Networking**  
   - **Internet Gateway** for connecting public subnets to the Internet.  
   - **NAT Gateway** in public subnet to allow outbound internet access for private subnet instances.  
   - **EC2 Instance Connect Endpoint** for secure access to instances in both public and private subnets.

3. **Security**  
   - **Security Groups** to act as virtual firewalls for instances.  
   - Web servers deployed in **private subnets** for improved security.  
   - Used **AWS Certificate Manager** for HTTPS encryption.

4. **Compute & Load Balancing**  
   - **EC2 instances** hosted in private subnets.  
   - **Application Load Balancer (ALB)** to distribute traffic evenly across multiple Availability Zones.  
   - **Auto Scaling Group** to manage instance scaling automatically.

5. **Monitoring & Notifications**  
   - **Amazon SNS** configured to send alerts for Auto Scaling activities.

6. **Domain & Storage**  
   - **Route 53** for domain registration and DNS configuration.  
   - **Amazon S3** for storing application code and migration scripts.

---

## 🗄️ Database Migration

Database migration was automated using **Flyway** and a migration script stored in **S3**.  
The script downloads the migration file from S3 and applies it to the **Amazon RDS MySQL** database.

**Migration Script**: [`migrate.sh`](./migrate.sh)  
Example:
```bash
#!/bin/bash

S3_URI=s3://dev-sql-bk/V1__shopwise.sql
RDS_ENDPOINT=dev-rds-db.cboj41rgnret.us-east-1.rds.amazonaws.com
RDS_DB_NAME=applicationdb
RDS_DB_USERNAME=akem
RDS_DB_PASSWORD=Newuser111

# Update packages
sudo yum update -y

# Download and extract Flyway
sudo wget -qO- https://download.red-gate.com/maven/release/com/redgate/flyway/flyway-commandline/11.11.0/flyway-commandline-11.11.0-linux-x64.tar.gz | tar -xvz

# Create SQL directory for migration files
sudo mkdir sql

# Download migration script from S3
sudo aws s3 cp "$S3_URI" sql/

# Run migration
flyway -url=jdbc:mysql://"$RDS_ENDPOINT":3306/"$RDS_DB_NAME" \
  -user="$RDS_DB_USERNAME" \
  -password="$RDS_DB_PASSWORD" \
  -locations=filesystem:sql \
  migrate
````

