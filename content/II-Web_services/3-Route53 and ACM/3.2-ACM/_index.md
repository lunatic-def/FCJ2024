---
title : "Create ACM"
date : "`r Sys.Date()`"
chapter : false
pre : " <b> 3.2 </b> "
---
[AWS Certificate Manage](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)

[ACM module](https://registry.terraform.io/modules/terraform-aws-modules/acm/aws/latest)

***ACM module to create and verify SSL certificate***
```
module "acm" {
  source  = "terraform-aws-modules/acm/aws"
  version = "5.0.0"

  domain_name  = trimsuffix(data.aws_route53_zone.mydomain.name, ".")
  zone_id      = data.aws_route53_zone.mydomain.zone_id 

  subject_alternative_names = [
    "*.langocanh.net"
  ]
  tags = { name = "application certificate manager"}
  # Validation Method
  validation_method = "DNS"
  wait_for_validation = true  
}

# Output ACM Certificate ARN
output "acm_certificate_arn" {
  description = "The ARN of the certificate"
  value       = module.acm.acm_certificate_arn
}

```

