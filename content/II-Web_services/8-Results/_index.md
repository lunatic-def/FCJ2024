---
title : "Results"
date :  "`r Sys.Date()`" 
weight : 8 
chapter : false
pre : " <b> 8. </b> "
---
1) EC2 instances:
![ec2](/FCJ2024/images/2.prerequisite/ec2.png)
- SSH connection from bation instance to private instance
![ec2](/FCJ2024/images/2.prerequisite/ec2SSH.png)
2) Auto scaling: 
![at](/FCJ2024/images/2.prerequisite/auto-scaling.png)
- launch template
![at](/FCJ2024/images/2.prerequisite/launch_template.png)
3) ALB:
![alb](/FCJ2024/images/2.prerequisite/Pictalb.png)
- listerners and rules: 
![alb](/FCJ2024/images/2.prerequisite/alb.png)
- target group:
![alb](/FCJ2024/images/2.prerequisite/targetgr.png)
4) KMS:
- policy and cryptographic configuration:
![kms](/FCJ2024/images/KMS/Picture1.png)
![kms](/FCJ2024/images/KMS/Picture2.png)
5) RDS database:
![rds](/FCJ2024/images/RDS/rds.png)
- Database storage enryption with KMS key:
![rds](/static/images/RDS/sec.png)

***PHP web service test run***
- Simple website for testing fucntion input and output from the RDS database

**Website before entering more value**
![web](/FCJ2024/images/web1.png)
**Database before entering more value**
- SSH into bastion instance -> run command to login to MYSQL sever on port 3306
```
mysql -h RDS_ENDPOINT -u DBADMIN -pDB_PASSWORD
```
![web](/FCJ2024/images/rds_connection.png)

**Website after entering more value**
![web](/FCJ2024/images/rds_conn2.png)
**Database after entering more value**
![web](/FCJ2024/images/rds3.png)