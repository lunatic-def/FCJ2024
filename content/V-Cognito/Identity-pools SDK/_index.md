---
title : "Identity-pools SDK"
date :  "`r Sys.Date()`" 
weight : 2 
---
SUMMARY 
1) Directory pools of federated identities -> exchange for AWS credentials 

    => BY generating temporary credentials AWS cre for users of your app (signed in or not)
2) IAM for choosing the level of permission that you want to grant to your users

    => CAN start out as a GUESTS.

    => Signed in with a third-party IdP to unlock access to assets that you make available to registered members.

**Features of Amazon Cognito identity pools**
***Sign requests for AWS services***
Sign API requests to AWS services like Amazon Simple Storage Service (Amazon S3) and Amazon DynamoDB. Analyze user activity with services like Amazon Pinpoint and Amazon CloudWatch.

***Filter requests with resource-based policies***
Exercise granular control over user access to your resources. Transform user claims into IAM session tags, and build IAM policies that grant resource access to distinct subsets of your users.

***Assign guest access***
For your users who haven’t signed in yet, configure your identity pool to generate AWS credentials with a narrow scope of access. Authenticate users through a single sign-on provider to elevate their access.

***Assign IAM roles based on user characteristics***
Assign a single IAM role to all of your authenticated users, or choose the role based on the claims of each user.

***Accept a variety of identity providers***
Exchange an ID or access token, a user pool token, a SAML assertion, or a social-provider OAuth token for AWS credentials.

***Validate your own identities***
Perform your own user validation and use your developer AWS credentials to issue credentials for your users.