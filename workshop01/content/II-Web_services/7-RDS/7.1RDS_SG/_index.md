---
title : "RDS MYSQL Security group"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 7.1 </b> "
---

<!-- {{% notice info %}}
**Port Forwarding** is a useful way to redirect network traffic from one IP address - Port to another IP address - Port. With **Port Forwarding** we can access an EC2 instance located in the private subnet from our workstation.
{{% /notice %}} -->

***Port 3306 for MySQL server connection***
```
# Security Group for AWS RDS DB
module "rdsdb_sg" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "5.1.0"

  name        = "rdsdb-sg"
  description = "Access to MySQL DB for entire VPC CIDR Block"
  vpc_id      = module.vpc.vpc_id

  # ingress
  ingress_with_cidr_blocks = [
    {
      from_port   = 3306
      to_port     = 3306
      protocol    = "tcp"
      description = "MySQL access from within VPC"
      cidr_blocks = module.vpc.vpc_cidr_block
    },
  ]
  # Egress Rule - all-all open
  egress_rules = ["all-all"]
  tags         = { name = "rds_secgroup" }
}
```