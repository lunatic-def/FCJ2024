---
title : "RDS MYSQL"
date :  "`r Sys.Date()`" 
weight : 7 
chapter : false
pre : " <b> 7.2 </b> "
---

<!-- {{% notice info %}}
**Port Forwarding** is a useful way to redirect network traffic from one IP address - Port to another IP address - Port. With **Port Forwarding** we can access an EC2 instance located in the private subnet from our workstation.
{{% /notice %}} -->


***RDS variable***
```
# DB Name
variable "db_name" {
  description = "AWS RDS Database Name"
  type        = string
}
# DB Instance Identifier
variable "db_instance_identifier" {
  description = "AWS RDS Database Instance Identifier"
  type        = string
}
# DB Username - Enable Sensitive flag
variable "db_username" {
  description = "AWS RDS Database Administrator Username"
  type        = string
}
# DB Password - Enable Sensitive flag
variable "db_password" {
  description = "AWS RDS Database Administrator Password"
  type        = string
  sensitive   = true
}
```
***Automating variable assign***
```
# RDS Database Variables
db_instance_identifier = "masterdb"
db_name                = "mywebdb"
db_username            = "mydbadmin"
db_password            = "mydbpass03"
```
***Master DB module***
```
################################################################################
# Master DB
################################################################################

# Create AWS RDS Database
module "master" {
  source  = "terraform-aws-modules/rds/aws"
  version = "6.3.0"
  #kms_key_id = 
  
  identifier = var.db_instance_identifier #masterdb

  db_name     = var.db_name #mywebdb
  username = var.db_username
  password = var.db_password
  manage_master_user_password = false # not using secret manager

  multi_az               = false # not using standby instance
  create_db_subnet_group = true 
  subnet_ids             = module.vpc.database_subnets
  vpc_security_group_ids = [module.rdsdb_sg.security_group_id]
  port = 3306

  engine               = "mysql"
  engine_version       = "8.0.35"
  family               = "mysql8.0" # DB parameter group
  major_engine_version = "8.0"      # DB option group
  instance_class       = "db.t4g.micro"

  allocated_storage     = 10
  max_allocated_storage = 20
  


  # maintenance_window              = "Mon:00:00-Mon:03:00"
  # backup_window                   = "03:00-06:00"
  # enabled_cloudwatch_logs_exports = ["general"]
 
  backup_retention_period = 7
  skip_final_snapshot     = true
  deletion_protection     = false
  storage_encrypted     = true
  kms_key_id = module.kms.key_arn  #KMS key for storage encryption

  # performance_insights_enabled          = true
  # performance_insights_retention_period = 7
  # create_monitoring_role                = true
  # monitoring_interval                   = 60

  parameters = [
    {
      name  = "character_set_client"
      value = "utf8"
    },
    {
      name  = "character_set_server"
      value = "utf8"
    }
  ]

  tags = {name = "master_mysql"}
  db_instance_tags = {
    "Sensitive" = "high"
  }
  db_option_group_tags = {
    "Sensitive" = "low"
  }
  db_parameter_group_tags = {
    "Sensitive" = "low"
  }
  db_subnet_group_tags = {
    "Sensitive" = "high"
  }
}

```
***Read replication DB module***
```
################################################################################
# Replica DB
################################################################################

module "replica" {
  source  = "terraform-aws-modules/rds/aws"
  version = "6.4.0"

  identifier = "replicadb"

  # Source database. For cross-region use db_instance_arn
  replicate_source_db = module.master.db_instance_identifier


  engine               = "mysql"
  engine_version       = "8.0.35"
  family               = "mysql8.0" # DB parameter group
  major_engine_version = "8.0"      # DB option group
  instance_class       = "db.t4g.micro"

  allocated_storage     = 10
  max_allocated_storage = 20

  port = 3306
  
  username = var.db_username
  password = var.db_password
  # Not supported with replicas
  manage_master_user_password = false

  multi_az               = false
  vpc_security_group_ids = [module.rdsdb_sg.security_group_id]
  subnet_ids = [module.vpc.database_subnets[1]]

  # maintenance_window              = "Tue:00:00-Tue:03:00"
  # backup_window                   = "03:00-06:00"
  # enabled_cloudwatch_logs_exports = ["general"]

  backup_retention_period = 0
  skip_final_snapshot     = true
  deletion_protection     = false
  storage_encrypted     = true
  kms_key_id = module.kms.key_arn #KMS key for storage encryption

  tags = { name = "replica_mysql" }
  parameters = [
    {
      name  = "character_set_client"
      value = "utf8mb4"
    },
    {
      name  = "character_set_server"
      value = "utf8mb4"
  }]

  db_instance_tags        = { "Sensitive" = "high" }
  db_option_group_tags    = { "Sensitive" = "low" }
  db_parameter_group_tags = { "Sensitive" = "low" }
  db_subnet_group_tags    = { "Sensitive" = "high" }
}
```