---
title : "Create Launch Template"
date : "`r Sys.Date()`"
weight : 1
pre : " <b> 5.1 </b> "
---
- [Launch template resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/launch_template)

```
# Launch Template Resource
resource "aws_launch_template" "my_launch_template" {
  name = "launch-template"
  description = "My Launch Template"
  image_id = data.aws_ami.amzlinux2.id
  instance_type = var.instance_type

  vpc_security_group_ids = [module.private-secgroup.security_group_id]
  key_name = var.instance_keypair  
  #user_data = filebase64("${path.module}/script.sh")
  user_data = base64encode(templatefile("${path.module}/script.tmpl",{rds_db_endpoint = module.master.db_instance_address}))  
  ebs_optimized = false # not support for t2.micro/ t3.micro uwu
  #default_version = 1
  update_default_version = true
#   block_device_mappings {
#     device_name = "/dev/sda1"
#     ebs {
#       volume_size = 10    
#       delete_on_termination = true
#       volume_type = "gp2" # default is gp2
#      }
#   }
  monitoring {
    enabled = false
  }

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "private_instance"
    }
  }
}
```
- Template for automating script for Web server -> PHP website, apache2
```
#! /bin/bash
sudo yum update -y
sudo yum install git -y
sudo yum install php php-pdo php-mysql -y
sudo yum install php-pdo php-mysqlnd -y
sudo amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
cd /var/www
git clone https://github.com/lunatic-def/temp.git
mkdir inc
mv /var/www/temp/db_config.inc /var/www/inc
mv /var/www/temp/Website.php /var/www/html
sudo systemctl restart httpd
```