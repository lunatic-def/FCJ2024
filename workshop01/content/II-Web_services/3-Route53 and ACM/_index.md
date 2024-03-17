---
title : "Route53 and ACM"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
{{% notice info %}}
You must manually register for a domain on Route53 for this step.
{{% /notice %}}

In this step we will configure "Simple routing protocol" for Route53 and use ACM for managing SSL certificate.
### Content
- [Application certificate manager module](https://registry.terraform.io/modules/terraform-aws-modules/acm/aws/latest)
- [Route53 resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route53_record)


