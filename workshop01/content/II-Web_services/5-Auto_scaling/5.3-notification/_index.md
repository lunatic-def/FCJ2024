---
title : "Create Auto Scaling Notification resource"
date : "`r Sys.Date()`"
weight : 3
pre : " <b> 5.3 </b> "
---
- [SNS resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sns_topic)
- [SNS topic subscription resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/sns_topic_subscription)
- [Auto-scaling notification resource](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/autoscaling_notification)

***Create Simple notification service***

```
## SNS - Topic
resource "aws_sns_topic" "myasg_sns_topic" {
  name = "myasg-sns-topic"
}

## SNS - Subscription
resource "aws_sns_topic_subscription" "myasg_sns_topic_subscription" {
  topic_arn = aws_sns_topic.myasg_sns_topic.arn
  protocol  = "email"
  endpoint  = "WinterterminalLOGG@gmail.com"
}
```

***Create auto-scaling notification rules***

```
## Create Autoscaling Notification Resource
resource "aws_autoscaling_notification" "myasg_notifications" {
  group_names = [aws_autoscaling_group.my_asg.id]
  notifications = [
    "autoscaling:EC2_INSTANCE_LAUNCH",
    "autoscaling:EC2_INSTANCE_TERMINATE",
    "autoscaling:EC2_INSTANCE_LAUNCH_ERROR",
    "autoscaling:EC2_INSTANCE_TERMINATE_ERROR",
  ]
  topic_arn = aws_sns_topic.myasg_sns_topic.arn 
}
```
- SNS topic subscription
![cw](/images/Cloudwatch/sns.png)