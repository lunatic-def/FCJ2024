---
title : "Create Route53"
date : "`r Sys.Date()`"
chapter : false
pre : " <b> 3.1 </b> "
---
***Simple routing policy***
```
#Simple routing policy
resource "aws_route53_record" "apps_dns" {
  zone_id = data.aws_route53_zone.mydomain.zone_id 
  name    = "apps.langocanh.net"
  type    = "A"
  alias {
    name                   = module.alb.dns_name
    zone_id                = module.alb.zone_id
    evaluate_target_health = true
  }  
}

```

