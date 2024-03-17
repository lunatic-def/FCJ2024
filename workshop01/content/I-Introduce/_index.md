---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> I. </b> "
---
In this work shop you will learn about creating a secure web service with MySQL database on AWS platform using terraform for automation of IAC (infrastructor as code).

![VPC](/images/1.intro/ws1.jpg)
- VPC:
    * 2 nat, 2 azs, 2 public subnets, 4 private subnets
- EC2:
    * 1 Bastion instance
    * 2 Private instances
    * 2 RDS database (MySQL)
- Services: Application load balencer, Auto-scaling, Launch templates, S3, Elastic IP address (EIP), Route53
- Monitoring and Security: 
    * Cloudwatch
    * SNS - Simple notification service
    * KMS - Key management service
    * CloudHSM 
    * IAM - Identity access management 
    * ACM - Application certificate manager
    * AWS Cognito (demo version only)
- Remote state file with state locking for preventing conflict during development
