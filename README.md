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
### Difference between EC2, ECS, EKS, and lambda? When would you use each?

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

## 2. AWS Security & IAM
## 3. CI/CD & Automation in AWS
## 4. Monitoring & Logging
## 5. Cost Optimization & Performance Tuning

# Chapter X. Other Domains
