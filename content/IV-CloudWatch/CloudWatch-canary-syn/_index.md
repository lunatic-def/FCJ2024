---
title : "CloudWatch Canary Syn"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
---

***IAM policy for managing cloudwatch Canary Synthetic -> S3 logs, Cloudwatch***
```
# AWS IAM Policy
resource "aws_iam_policy" "cw_canary_iam_policy" {
  name        = "cw-canary-iam-policy"
  path        = "/"
  description = "CloudWatch Canary Synthetic IAM Policy"

  # Terraform's "jsonencode" function converts a
  # Terraform expression result to valid JSON syntax.
  policy = jsonencode({
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "cloudwatch:PutMetricData",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "cloudwatch:namespace": "CloudWatchSynthetics"
                }
            }
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "logs:CreateLogStream",
                "s3:ListAllMyBuckets",
                "logs:CreateLogGroup",
                "logs:PutLogEvents",
                "s3:GetBucketLocation",
                "xray:PutTraceSegments"
            ],
            "Resource": "*"
        }
    ]
})
}

# AWS IAM Role
resource "aws_iam_role" "cw_canary_iam_role" {
  name                = "cw-canary-iam-role"
  description = "CloudWatch Synthetics lambda execution role for running canaries"
  path = "/service-role/"
  #assume_role_policy  = data.aws_iam_policy_document.instance_assume_role_policy.json # (not shown)
  assume_role_policy = "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"lambda.amazonaws.com\"},\"Action\":\"sts:AssumeRole\"}]}" 
  managed_policy_arns = [aws_iam_policy.cw_canary_iam_policy.arn]
}
```
- Canary role creation and policy
![cw](/FCJ2024/images/Cloudwatch/canary_role.png)
![cw](/FCJ2024/images/Cloudwatch/cw-policy.png)
***AWS CloudWatch Canary***
```
resource "aws_synthetics_canary" "sswebsite2" {
  name                 = "sswebsite2"
  artifact_s3_location = "s3://${module.s3_bucket.s3_bucket_id}/sswebsite2"
  execution_role_arn   = aws_iam_role.cw_canary_iam_role.arn 
  handler              = "sswebsite2.handler"
  zip_file            = "sswebsite2/sswebsite2.zip" 
  #zip_file             = filebase64("${path.module}/sswebsite2/sswebsite2v1.zip")
  runtime_version      = "syn-nodejs-puppeteer-7.0"
  start_canary = true

  run_config {
    active_tracing = true
    memory_in_mb = 960
    timeout_in_seconds = 60
  }
  schedule {
    expression = "rate(1 minute)"
  }
}
```
![cw](/FCJ2024/images/Cloudwatch/canary1.png)
![cw](/static/images/Cloudwatch/canary2.png)
- canary log 
![cw](/FCJ2024/images/Cloudwatch/canarylog.png)
- canary screenshot of web page
![cw](/FCJ2024/images/Cloudwatch/canaryscreenshot.png)
- canary S3 log storage blocking public access
![cw](/FCJ2024/images/Cloudwatch/canarys3.png)
![cw](/FCJ2024/images/Cloudwatch/s3.png)
- "sswebsite2" for cloudwatch canary UI.
![cw](/FCJ2024/images/Cloudwatch/s3-2.png)
