---
title : "Key management service"
date :  "`r Sys.Date()`" 
weight : 6 
chapter : false
pre : " <b> 6. </b> "
---
[Key management service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

[KMS module](https://registry.terraform.io/modules/terraform-aws-modules/kms/aws/latest)
- Creating Single-region key symmetric key for encrypting RDS database 

***Create IAM user to manage Key***
```
#allow access to key by "kmsuser"
data "aws_iam_policy_document" "rds_key" {
#This statement allows all users and services in this account to execute all KMS actions on this key
  statement {
    sid       = "Enable IAM User Permissions"
    effect    = "Allow"
    actions   = ["kms:*"]
    resources = ["*"]

    principals {
      type        = "AWS"
      identifiers = ["arn:aws:iam::${local.account_id}:root"]
    }
  }
#This statement allows selected individual users and IAM roles to fully manage this key. 
#This statement must exist for every single key. Without this statement you will absolutely lose access and management of this key.
  statement {
    sid       = "Allow access for Key Administrators"
    effect    = "Allow"
    actions   = ["kms:*"]
    resources = ["*"]

    principals {
      type = "AWS"
      identifiers = [
        "arn:aws:iam::${local.account_id}:user/${local.key_user}",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/support.amazonaws.com/AWSServiceRoleForSupport",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/trustedadvisor.amazonaws.com/AWSServiceRoleForTrustedAdvisor"
      ]
    }
  }
#This statement is specifically for the usage of the key. 
#If you do not provide the statement Enable IAM User Permissions then you must include this statement; 
#Otherwise this key will not be usable by anyone besides the key administrators.
  statement {
    sid    = "Allow use of the key"
    effect = "Allow"
    actions = [
      "kms:Encrypt",
      "kms:Decrypt",
      "kms:ReEncrypt*",
      "kms:GenerateDataKey*",
      "kms:DescribeKey"
    ]
    resources = ["*"]

    principals {
      type = "AWS"
      identifiers = [
        "arn:aws:iam::${local.account_id}:user/${local.key_user}",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/support.amazonaws.com/AWSServiceRoleForSupport",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/trustedadvisor.amazonaws.com/AWSServiceRoleForTrustedAdvisor"
      ]
    }
  }
#This statement allows listing, creating, and revoking grants for the key by the principals identified in the statement.
  statement {
    sid    = "Allow attachment of persistent resources"
    effect = "Allow"
    actions = [
      "kms:CreateGrant",
      "kms:ListGrants",
      "kms:RevokeGrant"
    ]
    resources = ["*"]

    principals {
      type = "AWS"
      identifiers = [
        "arn:aws:iam::${local.account_id}:user/${local.key_user}",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/support.amazonaws.com/AWSServiceRoleForSupport",
        "arn:aws:iam::${local.account_id}:role/aws-service-role/trustedadvisor.amazonaws.com/AWSServiceRoleForTrustedAdvisor"
      ]
    }

    condition {
      test     = "Bool"
      variable = "kms:GrantIsForAWSResource"
      values   = ["true"]
    }
  }
}
```

***KMS module***
```
module "kms" {
  source  = "terraform-aws-modules/kms/aws"
  version = "2.2.1"
  aliases = [ "rds_kms" ]

  deletion_window_in_days = 7 # for testing
  description             = "Complete key example showing various configurations available"
  enable_key_rotation     = false # for testing 
  is_enabled              = true
  key_usage               = "ENCRYPT_DECRYPT"
  multi_region            = false
  policy      = data.aws_iam_policy_document.rds_key.json 
  tags = {
    Name  = "rds_kms"
  }

}
```