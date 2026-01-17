# Lab 01 — Highly Available Web Application on AWS (EC2 + ALB + Auto Scaling)

## 📌 Overview
This lab demonstrates how to deploy a **highly available web application** across multiple Availability Zones using:

- Amazon EC2  
- Application Load Balancer (ALB)  
- Auto Scaling Group (ASG)  
- Security Groups  
- User Data scripts  

The architecture ensures **fault tolerance**, **scalability**, and **zero downtime** during instance failures.

---

## 🏗️ Architecture Diagram (Text Description)

```
User → Application Load Balancer → Target Group → EC2 Instances (AZ A & AZ B)
                                      ↑
                                      |
                             Auto Scaling Group
```

---

## 🎯 Objectives
- Deploy EC2 instances in multiple Availability Zones  
- Install and configure a web server  
- Create an Application Load Balancer  
- Register instances in a target group  
- Configure an Auto Scaling Group to maintain capacity  
- Test failover and scaling behavior  

---

## 🛠️ Steps Performed

### 1. Launched Two EC2 Instances
- Created `web-server-1` and `web-server-2`  
- Used Amazon Linux 2 AMI  
- Selected **t2.micro** (Free Tier eligible)  
- Placed each instance in a different Availability Zone  
- Created a security group allowing HTTP (port 80)  

---

### 2. Installed Apache Web Server
Connected via SSH and ran:

```bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
```

Created unique index pages:

```bash
echo "Hello from web-server-1 in AZ A" > /var/www/html/index.html
echo "Hello from web-server-2 in AZ B" > /var/www/html/index.html
```

Verified both pages using their public IPs.

---

### 3. Created an Application Load Balancer
- Configured an **Internet-facing** ALB  
- Selected two subnets (AZ A & AZ B)  
- Created a target group (`web-tg`)  
- Registered both EC2 instances  
- Verified health checks passed  
- Accessed ALB DNS name and confirmed traffic alternated between servers  

---

### 4. Created a Launch Template
Configured a launch template with:

- Amazon Linux 2  
- t2.micro  
- Security group  
- User Data script to auto-install Apache  

User Data:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "Hello from Auto Scaling instance" > /var/www/html/index.html
```

---

### 5. Created an Auto Scaling Group
- Created ASG named `web-asg`  
- Attached launch template  
- Selected two subnets  
- Attached to target group  
- Set desired capacity = 2  
- Set min = 2, max = 4  

---

### 6. Tested Auto Scaling
- Terminated one EC2 instance manually  
- ASG automatically launched a replacement instance  
- New instance registered with ALB  
- Application remained available with zero downtime  

---

## ✅ Results
- Application stayed online even when an instance was terminated  
- ALB successfully distributed traffic across AZs  
- Auto Scaling Group maintained desired capacity  
- Demonstrated fault tolerance and high availability  

---

## 🧠 What I Learned
- How ALB distributes traffic across multiple Availability Zones  
- How Auto Scaling Groups maintain application availability  
- How User Data automates server configuration  
- How target groups and health checks work  
- Why multi‑AZ architecture is essential for production workloads  

---

## 🧹 Cleanup
Deleted:

- Auto Scaling Group  
- Launch Template  
- Load Balancer  
- EC2 instances  
- Unused EBS volumes  

This ensured the environment remained **Free Tier safe**.

---

## 🧩 Skills Demonstrated
- EC2 provisioning  
- Load balancing  
- Auto scaling  
- High availability design  
- Security groups  
- Linux server configuration  
- AWS architecture best practices  
