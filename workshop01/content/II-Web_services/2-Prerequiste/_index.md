---
title : "Preparation "
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

To learn how to use terraform provider AWS:

[AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

***Define terraform version***
```
terraform {
  required_version = ">= 1.6" # which means any version equal & above 0.14 like 0.15, 0.16 etc and < 1.xx
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0"
    }
    null = {
      source = "hashicorp/null"
      version = "~> 3.0"
    }        
  }
}
```
***Define Provider***
- The "access key" and "secret key" is located in your account security credential
- This lab will use only "us-east-1" region
```
# Provider AWS
provider "aws" {
  region  = var.aws_region
  access_key = ""
  secret_key = ""
}
# AWS Region
variable "aws_region" {
  description = "Region in which AWS Resources to be created"
  type = string
  default = "us-east-1"  
}
```
### Content
  - [Create VPC](2.1-createec2/2.1.1-createvpc/)
  - [Create security group](2.1-createec2/2.1.2-createsecgroup/)
  - [Create EC2](2.1-createec2/2.1.3-createec2linux/)