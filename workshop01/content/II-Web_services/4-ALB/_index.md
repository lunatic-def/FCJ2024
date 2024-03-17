---
title : "Application Load Balencer"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

In this step, we will connect to our EC2 private servers to Application load balencer.
### Content
- [Application load balencer module](https://registry.terraform.io/modules/terraform-aws-modules/alb/aws/latest/) 

1. Create Security group for ALB 
```
module "alb-secgroup" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "5.1.0"


  name        = "alb_secgroup"
  description = "Security group which is used as an argument in complete-sg"
  vpc_id = module.vpc.vpc_id

  ingress_cidr_blocks = ["0.0.0.0/0"]
  # Website connection
  ingress_rules       = ["http-80-tcp","https-443-tcp"]
  egress_rules = ["all-all"]

    tags = {
    name = "ALB Security Group"
  }
}
```
2. Create ALB:
    * HTTP and HTTPS listener
    * Target group for private instances "mytg1"
    * ACM  for managing public and private SSL/TLS X.509 certificates and keys that protect AWS websites and applications
```
# Terraform AWS Application Load Balancer (ALB)
module "alb" {
  depends_on = [ module.vpc, module.master ]
  source  = "terraform-aws-modules/alb/aws"
  version = "9.5.0"

  name = "alb"
  load_balancer_type = "application"
  vpc_id = module.vpc.vpc_id
  subnets = module.vpc.public_subnets

  security_groups = [module.alb-secgroup.security_group_id]

  # For example only
  enable_deletion_protection = false
  # Listeners
  listeners = {
    #HTTP redirect to HTTPS 
    http_tcp_listeners = {
      port               = 80
      protocol           = "HTTP"
      action_type = "redirect"
      redirect = {
        port        = "443"
        protocol    = "HTTPS"
        status_code = "HTTP_301"
      }
    }#END of HTTP

    #HTTPS
    https_listeners = {     
    # HTTPS Listener Index = 0 for HTTPS 443  
      port               = 443
      protocol           = "HTTPS"
      #default ssl_policy = ELBSecurityPolicy-2016-08
      ssl_policy         = "ELBSecurityPolicy-TLS13-1-2-Res-2021-06"
      certificate_arn    = module.acm.acm_certificate_arn
      action_type = "fixed-response"

      forward = {
        target_group_key = "mytg1"
      }


      fixed-response = {
        actions = [{
          type         = "fixed-response"
          content_type = "text/plain"
          status_code  = 200
          message_body = "This is a fixed response"
        }]
      }
      #RULES
      rules = {
        myapp-rule = {
          priority = 1
          actions = [{
            type = "weighted-forward"
            target_groups = [
            {
              target_group_key = "mytg1"
              weight           = 1
            }
            ]
            stickiness = {
              enabled  = true
              duration = 3600
            }
          }]
          conditions = [{
            path_pattern = {values = ["/*"]}
          }]
        }# End of myapp1-rule
    }#END of rules

  }# END HTTPS
}

# Target Groups
  target_groups = {
   mytg1 = {     
      create_attachment = false 
      name_prefix                       = "mytg1-"
      protocol                          = "HTTP"
      port                              = 80
      target_type                       = "instance"
      deregistration_delay              = 10
      load_balancing_cross_zone_enabled = false
      protocol_version = "HTTP1"
      health_check = {
        enabled             = true
        interval            = 30
        path                = "/"
        port                = "traffic-port"
        healthy_threshold   = 2
        unhealthy_threshold = 2
        timeout             = 6
        protocol            = "HTTP"
        matcher             = "200-399"
      }
      tags = {name = "target group for alb"}
  } #mytg1
  }# END target_groups Block
}#alb end
```
