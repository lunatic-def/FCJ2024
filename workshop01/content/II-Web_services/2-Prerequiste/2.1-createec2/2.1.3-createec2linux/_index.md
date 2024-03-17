---
title : "Create EC2 instances"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.1.3 </b> "
---
EC2 module:
  * [EC2 module](https://registry.terraform.io/modules/terraform-aws-modules/ec2-instance/aws/latest)

***1. Declare AMI data:***
```
# Get latest AMI ID for Amazon Linux2 OS
data "aws_ami" "amzlinux2" {
  most_recent = true
  owners = [ "amazon" ]
  filter {
    name = "name"
    values = [ "amzn2-ami-hvm-*-gp2" ]
  }
  filter {
    name = "root-device-type"
    values = [ "ebs" ]
  }
  filter {
    name = "virtualization-type"
    values = [ "hvm" ]
  }
  filter {
    name = "architecture"
    values = [ "x86_64" ]
  }
}
```
***2. Bastion instance:***
```
module "bastion-instance" {
  source  = "terraform-aws-modules/ec2-instance/aws"
  version = "5.6.0"
  depends_on = [ module.vpc]
  name = "bastion-instance"
  ami = data.aws_ami.amzlinux2.id
  instance_type = var.instance_type
  key_name = var.instance_keypair
  user_data = file("${path.module}/jumpbox_install.sh") #use for checking the db connection 
  vpc_security_group_ids = [ module.bastion-secgroup.security_group_id]
  subnet_id =  module.vpc.public_subnets[0]
  tags = { name="bastion-instance"}
}
```
- Script for automatically execute in bastion instance -> install necessary components to connect to DB
```
#! /bin/bash
sudo yum update -y
sudo rpm -e --nodeps mariadb-libs-*
sudo amazon-linux-extras enable mariadb10.5 
sudo yum clean metadata
sudo yum install -y mariadb
sudo mysql -V
sudo yum install -y telnet
```
- EIP for remote ssh connection: 
```
resource "aws_eip" "eip1" {
  depends_on = [ module.bastion-instance, module.vpc]
  tags = {name="eip1"}

  instance = module.bastion-instance.id
  domain = "vpc"
}

```
***3. Private instances: (Simple way of creating private instances but in this lab we will you launch template instead)***
```
# module "private-instances" {
#   source  = "terraform-aws-modules/ec2-instance/aws"
#   version = "5.6.0"


#   name = "private-instance"
#   depends_on = [ module.vpc ] # need to wait for vpc to be created completely
#   ami = data.aws_ami.amzlinux2.id
#   instance_type          = var.instance_type
#   key_name               = var.instance_keypair
#   user_data = file("${path.module}/script.sh")
#   #monitoring             = true
#   vpc_security_group_ids = [module.private-secgroup.security_group_id]
#   #use for subnet [10.0.1.0/24,10.0.2.0/24]
#   for_each = toset(["0", "1"]) #create multiple EC2 instances in each subnet
#   subnet_id =  element(module.vpc.private_subnets, tonumber(each.key))

#   tags = {
#     name = "Web_Server"
#   }
# }
```
-  Script for automatically execute in private instance -> **user_data**

***Result***
![VPC](/images/2.prerequisite/ec2.png)