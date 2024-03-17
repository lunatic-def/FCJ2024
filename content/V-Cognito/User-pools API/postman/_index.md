---
title : "Postman"
date :  "`r Sys.Date()`" 
weight : 3
---
Create tab call [CUP] 1. Get CUP token with /oauth2/token. click on the Authorisation item, then change the type to OAuth 2.0
![ps](/FCJ2024/images/Cognito/postmantoken1x.jpg)
Fill out the Authentication with the following:
Callback URL will be: https://aws.training 

The Auth URL’s will be: https://{your-cognito-domain}.auth.{your-region}.amazoncognito.com/oauth2/authorize 

and 

https://{your-cognito-domain}.auth.{your-region}.amazoncognito.com/oauth2/token 

This can be found by navigating in Cognito console to App integration tab. Then in there, look for Cognito domain. The Client ID can be found by scrolling down in the App integration page to App client and analytics section. The ID will be listed next to your App client name.

Then Press **"Get New Access Token"** at the bottom.
![ps](/FCJ2024/images/Cognito/postmantoken1.jpg)
This will pop up a mini browser requesting your credentials, use the credentials you created earlier to login and click Sign in.
![ps](/FCJ2024/images/Cognito/postman3.jpg)
You will receive the Token ID and Access token:
![ps](/FCJ2024/images/Cognito/postmantoken.jpg)
**TOKEN ID**
![ps](/FCJ2024/images/Cognito/postmantokenid.jpg)
**ACCESS TOKEN**
![ps](/FCJ2024/images/Cognito/accesstoken.jpg)