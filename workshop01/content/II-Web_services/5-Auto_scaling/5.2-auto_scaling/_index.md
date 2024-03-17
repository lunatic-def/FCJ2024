---
title : "Create Auto Scaling Group resource"
date : "`r Sys.Date()`"
weight : 2
pre : " <b> 5.2 </b> "
---
- [Auto-scaling resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/autoscaling_group)

```
resource "aws_autoscaling_group" "my_asg" {
  # auto scaling group auto matically create new instance when deploy without associating the previous instance so
  # no need to use Web server module
  depends_on = [ module.vpc, module.master ] # should create the db first for connection
  name_prefix = "myasg-"
  desired_capacity   = 2
  max_size           = 2
  min_size           = 2
  vpc_zone_identifier  = module.vpc.private_subnets
  # assign to ALB target group
  target_group_arns = [module.alb.target_groups["mytg1"].arn] 

  health_check_type = "EC2"
  #health_check_grace_period = 300 # default is 300 seconds  
  # Launch Template
  launch_template {
    id      = aws_launch_template.my_launch_template.id
    version = aws_launch_template.my_launch_template.latest_version
  }

  instance_maintenance_policy {
    min_healthy_percentage = 60
    max_healthy_percentage = 100
  }

  # Instance Refresh
  instance_refresh {
    strategy = "Rolling"
    preferences {
      instance_warmup = 300 # Default behavior is to use the Auto Scaling Group's health check grace period.
      min_healthy_percentage = 50
    }
    triggers = [ /*"launch_template",*/ "desired_capacity" ] # You can add any argument from ASG here, if those has changes, ASG Instance Refresh will trigger
  } 


  tag {
    key                 = "Owners"
    value               = "Web-Team"
    propagate_at_launch = true
  }      
}



output "zz" {
  value= aws_autoscaling_group.my_asg.target_group_arns
}
```
