---
title : "Results"
date :  "`r Sys.Date()`" 
weight : 8 
chapter : false
pre : " <b> 8. </b> "
---
1) EC2 instances:
![ec2](/images/2.prerequisite/ec2.png)
- SSH connection from bation instance to private instance
![ec2](/images/2.prerequisite/ec2SSH.png)
2) Auto scaling: 
![at](/images/2.prerequisite/auto-scaling.png)
- launch template
![at](/images/2.prerequisite/launch_template.png)
3) ALB:
![alb](/images/2.prerequisite/Pictalb.png)
- listerners and rules: 
![alb](/images/2.prerequisite/alb.png)
- target group:
![alb](/images/2.prerequisite/targetgr.png)
4) KMS:
- policy and cryptographic configuration:
![kms](/images/KMS/Picture1.png)
![kms](/images/KMS/Picture2.png)
5) RDS database:
![rds](/images/RDS/rds.png)
- Database storage enryption with KMS key:
![rds](/images/RDS/sec.png)

***PHP web service test run***
- Simple website for testing fucntion input and output from the RDS database

**Website before entering more value**
![web](/images/web1.png)
**Database before entering more value**
- SSH into bastion instance -> run command to login to MYSQL sever on port 3306
```
mysql -h RDS_ENDPOINT -u DBADMIN -pDB_PASSWORD
```
![web](/images/rds_connection.png)

**Website after entering more value**
![web](/images/rds_conn2.png)
**Database after entering more value**
![web](/images/rds3.png)