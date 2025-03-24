# DevOps

...

# Table of Contents

- **[Chapter I – Programming Languages](#chapter-i-programming-languages)**
- **[Chapter II – OS Concepts](#chapter-ii-os-concepts)**
- **[Chapter III – Managed Servdfders](#chapter-iii-managed-servers)**
- **[Chapter IV – Network Security](#chapter-iv-network-security)**
- **[Chapter V – Server Setddup](#chapter-v-server-setup)**
- **[Chapter VI – Infrastructure as Code](#chapter-vi-infrastructure-as-code)**
- **[Chapter VII – CICD](#chapter-vii-cicd)**
- **[Chapter VIII – Monitoring](#chapter-viii-monitoring)**
- **[Chapter IX – Cloud Providers](#chapter-ix-cloud-providers)**
    - [1. AWS Infrastructure and Networking (Ch. 18, 19, 27)](#1-aws-infrastructure-and-networking)
    - [2. AWS Security & IAM](#2-aws-security--iam)
    - [3. CI/CD & Automation in AWS](#3-cicd--automation-in-aws)
    - [4. Monitoring & Logging](#4-monitoring--logging)
    - [5. Cost Optimization & Performance Tuning](#5-cost-optimization--performance-tuning)
- **[Chapter X – Other Domains](#chapter-x--other-domains)**


# Chapter I. Programming Languages

# Chapter II. OS Concepts

# Chapter III. Managed Servers

# Chapter IV. Network Security

# Chapter V. Server Setup

# Chapter VI. Infrastructure as Code

# Chapter VII. CICD

# Chapter VIII. Monitoring

# Chapter IX. Cloud Providers
## 1. AWS Infrastructure and Networking
### 1.1 Difference between EC2, ECS, EKS, and lambda? When would you use each?

#### ECS vs EKS – Simple Overview

Think of ECS and EKS as two ways to run containers on AWS:

- ECS is AWS's own container service. Simple and native to AWS. Great when you want to run containers without much setup.
- EKS is Kubernetes on AWS. More flexible and powerful, but requires more knowledge and effort to set up.

Key differences:

- ECS is easier to use, but locked into AWS.
- EKS supports Kubernetes features like Helm, RBAC, CRDs, GitOps (e.g. ArgoCD), and service mesh (e.g. Linkerd, Istio).
- ECS is like renting a fully furnished apartment – AWS takes care of most things.
- EKS is like owning a house – you have full control, but you manage everything yourself.

---
#### ECS (Elastic Container Service)

What it is:  
A simple, AWS-native container orchestration service.

How it works:  
- You define tasks (containers)
- AWS handles networking, scaling, scheduling
- Can run on EC2 (you manage the servers) or Fargate (AWS manages servers)

When to use:  
- You want a simple solution
- You don’t need multi-cloud support

Example:  
Running a small app where you just want things to "run". ECS + Fargate is like using a food delivery app – you deploy, AWS delivers.

---

#### EKS (Elastic Kubernetes Service)

What it is:  
A managed Kubernetes cluster on AWS.

How it works:  
- Full Kubernetes environment
- Can work across clouds or on-prem
- You manage worker nodes or use Fargate

When to use:  
- You already use Kubernetes
- You need advanced features or multi-cloud flexibility
- You have a DevOps team familiar with Kubernetes

Example:  
Running a complex system with many services. You want control over networking, scaling, and cross-cloud support.

---

#### EC2 – Elastic Compute Cloud

EC2 is like renting a server in the cloud.  
You get full control over the OS, networking, and software.  
Like owning a PC – you install and configure what you want, but you also have to maintain it (updates, security, scaling, etc.)

---

#### Lambda – Serverless Compute
Lambda is a serverless computing service.
You upload code, and AWS runs it when triggered.  
No server to manage. No infrastructure to maintain.  
You pay only for actual usage.

### 1.2. How to design a highly available and scalable architect on AWS?
To build a HA and scalable system on AWS, we need focus on load balancing, multi-az deployments, auto-scaling group, master-slave architect of database.

Design example:
- User types “www.example.com” -> route 53 resolve DNS
- Route 53 redirect request to Application Load Balancer
- ALB routes traffics to an EC2 server running in multi-AZ (thoses server stay in ASG to scale in and out when needed)
- The application fetches data from the database (Multi-AZ with master and read replicas or all master)

### 1.3. How AWS VPC work? what is subnets, route tables, Internet Gateway, NAT gateway, and security groups?
- VPC is your own private network inside AWS where you can deploy resources like EC2, RDS, ECS, . It allow you to define IP ranges, subnet, security groups, …
- Subnet can divide to 2 type, public and private. Public subnet can access from the internet (e.g. Webserver) Private subnet cann’t be access directly from internet (e.g. database)
- Route table: defines rules for routing traffic between subnets, internet, and external networks.
- Internet gateway: - Connects the VPC to the public internet
- NAT Gateway - Secure Outbound internet access - Allows private subnet instances to access the internet without exposing them.
- Network ACL: control traffic at subnet level, it is stateless (you must allow both inbound and outbound rules separately)

### 1.4. Difference between Security Groups and NACLs in AWS?
- Security Groups: act as firewall for EC2 instance, work at instance level. it is stateful
- Example flow: User -> Route 53 -> ALB -> EC2 (public subnet) -> RDS (private subnet). If private instances need updates, they go through a NAT gateway.

### 1.5. How to establish a secure connection between AWS and an on-premise datacenter?
AWS provide several solution:
- AWS site-to-site VPN
- AWS transit gateway
- AWS direct connect

Site-to-site VPN example:
- AWS vpc is created, on-pre network has IP range 192.168.1.0/24
- a Virtual private gateway is attached to the VPC, 
- a Customer Gateway is setup on-pre network (it is on-pre vpn device, or virtual server). Then setup at both side t connect

### 1.6. How does AWS Transit Gateway work?
AWS Transit gateway helps to connect between multiple VPC, and on-prem networks.

Before transit gateway, VPC Peering was the primary ways to connect VPCs, but it has many downside (it need multiple connection, no centralize routing - need to configure routing at each VPC)

Transit gateway help to resolve that. It is on central hub ,can connect multiple VPCs, easy scaling, and efficient routing with single route table.

### 1.7. How to setup a multi-region deployment and what challenges you might face?
Example flow
- User request -> route 53 routes them to the closest AWS region based on latency.
- Application Layer:
User hit ALB in the nearest region
ALB forwards the request to ECS cluster running web server
- Database layer: primary DRS in region A with read replicas in region B for failover.
- If region A fail, route 53 automatically redirect traffic to region B.

**Challenge may facing:**
Data consistency - syncing data between region is complex
Latency issues - request may hit far-away regions. -> we can use route 53 latency-based routing.
Cost - Muti -region replica increase aws cost -> only replicate critical service.
Failover and DR testing must be implement in detail and concise

## 2. AWS Security & IAM
## 3. CI/CD & Automation in AWS
## 4. Monitoring & Logging
## 5. Cost Optimization & Performance Tuning

# Chapter X. Other Domains
