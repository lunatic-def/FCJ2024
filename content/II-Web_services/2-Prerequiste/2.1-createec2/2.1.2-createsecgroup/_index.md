---
title : "Create security groups"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

#### Create security groups

In this step, we will proceed to create the security groups used for our instances. As you can see, these security groups will not need to open traditional ports to **ssh** like port **22**, **MySQL** through port **3306** and **CloudHSM** through port  **2221 - 2225**

#### Create security group for Linux instance located in public subnet

Security group module for Public, Private and Databse subnets
   * [Security group module](https://registry.terraform.io/modules/terraform-aws-modules/security-group/aws/latest)

1. Public security group:
```
module "bastion-secgroup" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "5.1.0"

#SSH -> 0.0.0.0/0
#HSM/EC2 -> Internal
  name        = "bastion-sg"
  description = "Security group which is used as an argument in complete-sg"
  vpc_id = module.vpc.vpc_id
  # SSH to internal and external instances
  ingress_cidr_blocks = ["0.0.0.0/0"]
  ingress_rules       = ["ssh-tcp"]
  # Connect to CloudHSM service internal
  ingress_with_cidr_blocks = [
  {
    from_port   = 2221
    to_port     = 2225
    protocol    = "tcp"
    description = "Allow Port 2225 to connect to CloudHSM"
    cidr_blocks = module.vpc.vpc_cidr_block
  },
  ]
  egress_rules = ["all-all"]

    tags = {
    name = "Bastion Security Group"
  }
}
```
2. Private security group:
```
module "private-secgroup" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "5.1.0"


  name        = "private-sg"
  description = "Security group which is used as an argument in complete-sg"
  vpc_id = module.vpc.vpc_id

  # ALB and SSH internal connection
  ingress_rules       = ["http-8080-tcp", "ssh-tcp","http-80-tcp"]
  ingress_cidr_blocks = [module.vpc.vpc_cidr_block]

  egress_rules = ["all-all"]

    tags = {
    name = "Private Security Group"
  }
}
```