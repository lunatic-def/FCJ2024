---
title : "Case 1 demo"
date :  "`r Sys.Date()`" 
weight : 1 
---
***1. Create and config user pools***
- Case 1 demo with Cognito only Hosted UI and OAuth Server
- All of these configuration is the same as the manual config page
```
resource "aws_cognito_user_pool" "user_pool" {
  name = "user_pool"
  username_configuration {
    case_sensitive = true
  }
  alias_attributes = ["email"]
#   password_policy {
#     minimum_length = 6
    
#   }

  #mfa_configuration = "OFF" default
  verification_message_template {
    default_email_option = "CONFIRM_WITH_CODE"
    email_subject = "Account Confirmation"
    email_message = "Your confirmation code is {####}"
  }
  email_configuration {
    email_sending_account = "COGNITO_DEFAULT"
  }

 schema {
    attribute_data_type      = "String"
    developer_only_attribute = false
    mutable                  = true
    name                     = "email"
    required                 = true

    string_attribute_constraints {
      min_length = 1
      max_length = 256
    }
  }
  account_recovery_setting {
    recovery_mechanism {
      name     = "verified_email"
      priority = 1
    }
  }
  #Set to False if users can sign themselves up via an app. (base on APP need)
  admin_create_user_config {
    allow_admin_create_user_only = false
  }
  # verifying attribute changes
  auto_verified_attributes = ["email"]
  user_attribute_update_settings {
    attributes_require_verification_before_update = ["email"]
  }
  
}
```

- Cognitor default domain for authentication (Identity provider)
```
#Cognitor domain for testing 
resource "aws_cognito_user_pool_domain" "main" {
  domain       = "myupool"
  user_pool_id = aws_cognito_user_pool.user_pool.id
}
#Cognito authentication hosted UI
resource "aws_cognito_user_pool_ui_customization" "example" {
  css        = ".label-customizable {font-weight: 400;}"
  # Refer to the aws_cognito_user_pool_domain resource's
  # user_pool_id attribute to ensure it is in an 'Active' state
  user_pool_id = aws_cognito_user_pool_domain.main.user_pool_id
}
```
- Resource pool for OAuth 2.0 Client
```
resource "aws_cognito_resource_server" "resource" {
  identifier = "api_server" #for test
  name       = "api_server"

  user_pool_id = aws_cognito_user_pool.user_pool.id
  scope {
    scope_name = "read"
    scope_description = "get all items"
  }
}
```
***2. Create and config client pools***
- Create a user pool client with Cognito as the identity provider is Cognito API request made from user systems that are not trusted with a client secret
```
resource "aws_cognito_user_pool_client" "client" {
  name = "client_pool"
  callback_urls = ["https://aws.training"] #after sign in success you will be redirect to this urls
  logout_urls = ["https://localhost"] # urls for logout redirect

  

  allowed_oauth_flows_user_pool_client = true
  allowed_oauth_flows                  = ["code", "implicit"]
  allowed_oauth_scopes                 = ["profile", "openid"]
  supported_identity_providers         = ["COGNITO"]

  user_pool_id = aws_cognito_user_pool.user_pool.id
  generate_secret = true # for testing
  refresh_token_validity = 90
  prevent_user_existence_errors = "ENABLED"

  
  explicit_auth_flows = [
    "ALLOW_REFRESH_TOKEN_AUTH",
    "ALLOW_USER_SRP_AUTH",
    "ALLOW_ADMIN_USER_PASSWORD_AUTH"
  ]
  
}
```
