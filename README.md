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
### 2.1. How do you manage IAM roles, users, and policies in a large organization?
AWS organization:
- AWS account A (Dev team)
    - IAM user: dev 1
    - IAM role: include many policy: full access EC2 + full access S3
    - Policy: read-only S3
- AWS account B (DevOps)
    - IAM user: Devops 1
    - IAM role: EC2-ROLE
    - Policy: fulll access S3
- AWS account C (Finance)
    - IAM user: finance 1
    - IAM role: full access cost explorer

### 2.2. What are AWS KMS  (Key Management Service) and Secret Manager, and how do they differ?
- AWS KMS encrypt data at rest, such as S3 object, using managed encryption keys.
- AWS Secret Manager store credentials securely (like RDB passwords) and can auto-rotate them.
- If you need to store sensitive credential securely, use secret manager. 

### 2.3. How do you enforce least privilege access in AWS?
- The principle of least privilege means giving users, roles and services only the permission they need - nothing more. This reduce security risks.
- We use IAM Roles instead of users, grant only necessary permissions via fine-grained IAM policies, enfore MFA

### 2.4. How does **AWS GuardDuty, AWS Config and AWS Inspector** help with security?
- AWS GuardDuty detect real-time security like unauthorized access
- AWS Config ensures resources follow security policis by detecting misconfiguration
- AWS Inspector scan EC2 instance and container for vulnerability.

Together, they provide proactive and continuous AWS security monitoring

| AWS Service     | Nghĩa Tiếng Việt                        | Chức Năng                                                                 |
|-----------------|-----------------------------------------|---------------------------------------------------------------------------|
| AWS GuardDuty   | Bảo vệ - Canh gác - Giám sát an ninh    | Phát hiện **mối đe dọa bảo mật** trong tài khoản AWS, như truy cập trái phép, hành vi đáng ngờ. |
| AWS Config      | Cấu hình - Kiểm soát tài nguyên AWS     | Theo dõi **cấu hình AWS**, đảm bảo tuân thủ bảo mật, kiểm tra ai thay đổi tài nguyên.         |
| AWS Inspector   | Thanh tra - Kiểm tra bảo mật            | **Quét lỗ hổng bảo mật** trong EC2, container, ứng dụng để tìm điểm yếu (CVE, cấu hình sai).  |

### 2.5. How would you secure an S3 bucket that contains sensitive data?
- Block public access - Prevent unauthorized access
- Use IAM Policies - Grant least privilege access
- Encrypt Data (SSE-KMS) - Protect data at rest

### 2.6. What is AWS WAF and how does it protect your infrastructure?
**Secenario**: A company host a web application on EC2 behind an ALB and wants to:
- Block SQL injection and XSS attacks
- Limit requests from abusive users (rate-limiting)
- Block traffic from high-risk countries.

**Solution**: AWS WAF. Steps setup example
- Create nd AWS WAF and Web ACL
- Attach Web ACL to ALB
- Apply AWS Managed Rules (SQLi, XSS, Bot Control)
- Add Rate-Limiting & Geo-Blocking rules
- Monitor blocked requests using AWS WAF logs

## 3. CI/CD & Automation in AWS
### 3.1. How would you design a CI/CD pipeline using AWS services?
Scenario: A company wants to deploy a containerized application to Amazon ECS (Fargate) every time a developer pushes code to a GitHub repository.

**CI/CD Flow on AWS:**
- Developer push code to Github -> Trigger the pipeline
- AWS Code pipeline detect the changed and start execute the pineline
- AWS CodeBuild:
    + Pull code from github
    + Build a Docker Image and push to ECR
- AWS CodeDeploy:
    + Deploy new docker image to ECS
- Cloudwatch:
    + Monitor logs and performance

Pipeline completes, and the new version is live

### 3.2. What is AWS Code Pipeline, and how does it integrate with CodeBuild, CodeDeploy and CodeCommit?
**Flow Acitivity**
- Developer push code lên CodeCommit -> Code Pipeline trigger
- CodeBuild pull code from CodeCommit, run test, build, export packing file
- CodeDeploy get the packing file and deploy to EC2 with green/blue deployment
- CloudWatch & SNS sent notification if the deploy success/fail

**Notes**
- AWS Code Pipeline is automitically service for build, test, and implement appliation quickly.
- It integrate with CodeCommit to get source code, CodeBuild to test and compile
- CodeDeploy to deploy the packing file to EC2/ECS or lambda.
- When developer push code, pipeline automatically run and deploy the update

### 3.3. How can you automate infrastructure provisioning in AWS?
- In AWS, automate the infrastructure (Infastructure as Code - IaC) help to create, manage and replace the AWS resource effectively.
- Instead of manually config on AWS console, you can write code to deploy and managed the infrastructure

The automate infrustructure tools on AWS:

| Tools     | Description                        | When to use                                                              |
|-----------------|-----------------------------------------|---------------------------------------------------------------------------|
| AWS CloudFormation   | IaC service of AWS.    | When we need to deploy native infrustructure |
| Terraform      | Popular Iac Service, support multi-cloud (AWS, GCP, Azure)     | When need to deploy on multi platform|

### 3.4. What is difference between CloudFormation and Terraform? Which one would you choose and why?
- Both AWS CloudFormatoin and Terrafrom are IaC tools that automate AWS resource provisioning. However, they have key differences in functionality, flexibility and multi-cloud support
- AWS CloudFormation is AWS-native. Terraform, on the other hand, is multi-cloud on AWS, I'd use CloudFormation for simplicity. But for multi-cloud deployemnts or more flexibility, Terraform is better choice

### 3.5. How to handle blue/green deployments or canary releases in AWS?
- Blue/Green is strategy in which two enviroments (Blue & Green) run in parallel:
    - Blue (current) -> Run the old version of the application
    - Green (new) -> Run the new version of the application
    - Process:
        - Deploy the new version (green) in the new env without affecting users.
        - Test on Green to ensure there are no errors
        - Switch traffic from Blue to Green by udating DNS, Load Balancer or Route 53.
        - If there are errors, quickly rollback to Blue
- Canary Release is a strategy to deploy parts of the new version to a small group of users before expanding the entire version.
    - Process:
        - Deploy new version but only send 5-10% of traffic to it.
        - Monitor performance and check for errors.
        - If stable, increase traffice from 50% - 100%
        - If there are errors, rollback to old deployment

### 3.6. How does AWS Elastic Beanstalk comapre to EKS? 
- Elastic Beanstalk is a PaaS (Platform as a Service) help developer implement the application without manage the infrastructure.
- Main feature: easy to use - just upload code, AWS take care of the rest (server, scaling, load balancing)

## 4. Monitoring & Logging
### 4.1. How to setup monitoring and logging for a production environment in AWS?
a. monitoring
- CloudWatch Metric (similar to Prometheous) -> monitor CPU, RAM, Network, custume metrics
- CloudWatch Alarm -> send alert when exceed the threshold

b. logging
- CloudWatch logs: Store and analyze logs
- AWS CloudTrail (similar auditd) -> write log API and event in AWS
- Opensearch (similar ELK stack/ splunk) -> Store logs from EC2, lambda, ECS, ...

### 4.2. What are the key differences between AWS CloudWatch Logs, Metrics, Alarms:
- CloudWatch Logs: Store and analyze logs
- CloudWatch Metrics: Monitor CPU, RAM, network, latency, ...
- CloudWatch Alarms: Trigger alert when exceed threshold

### 4.3. What is AWS event bridge
- It help detect and response to the event of AWS in real time. It listen to the event in AWS and trigger the corresponse actions. 
- Process:
    - AWS service create event (e.g. EC2 instance is stopped, one file is upload to S3)
    - CloudWatch events detect the event based on rules.
    - Trigger action like lambda, SNS, SQS

### 4.4. How does AWS X-Ray help with application performance monitoring?
- X-Ray similar to Jaeger tool
- Application send trace data (request infor, response, delay)
- X-ray using those information to monitor the application, support troubleshoot, debug

### 4.5. What strategies for log aggregation and analysis in AWS?
- Process implement log aggregation and analysis
    - Application runing on EC2, Lambda, ECS -> write logs into CloudWatch Logs.
    - Logs data is sent to Amazon Kinesis Firehose
    - Firehose sent logs to S3/Opensearch to store and analysis
    - Analize logs with Opensearch or Athena
- Summary:
    - Logs aggregation: CloudWatch logs (real-time), S3 (longtime storage), Opensearch(searching logs)
    - Logs analyze: Opensearch/kibana

### 4.6. How to troubleshoot high latency or performance issues in AWS?
- We need to find the root cause, monitor the metrics and optimize system
- Solution for optimize system and reduce the latency
    - If the EC2 server is high cpu, ram:
        - Increase size of instance
        - Using auto scaling to add/remove instance
    - If Loadbanacer slow
        - Inspect the target and distribute the traffic resonalby
    - If Database is slow
        - Use Read Replicas (RDS) or DynamoDB to reduce the traffic
        - Using ElasticCache to cache query
        - Optimize index and queries on RDS
- Summary:
    - Using CloudWatch to montior CPU, RAM, Network when EC2 or Database slow
    - Using AWS X-Ray to analyze the latency of API Gateway, lambda, ECS/EKS
    - Using Load Balancer to detect the high reponse time
    - Optimize by using Auto Scaling, Caching, Read Replicas

## 5. Cost Optimization & Performance Tuning

# Chapter X. Other Domains
