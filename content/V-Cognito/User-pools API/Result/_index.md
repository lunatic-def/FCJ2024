---
title : "Result"
date :  "`r Sys.Date()`" 
weight : 2
---
***3. Result***

**1. User pools**
**Using the cognito default authenticate behavior, in the next lab i will try to do with other identity provider**
![up](/static/images/Cognito/ter1.png)
- Sign-in experience
![up](/static/images/Cognito/ter2.png)
- Sign-up experience:
![up](/static/images/Cognito/ter3.png)
- Messaging 
![up](/static/images/Cognito/ter4.png)
- App integration
![up](/static/images/Cognito/ter5.png)

**2. Client pools**
**NOTE** there is s client secret but in this picture i turn it off, you should config cs.
![up](/static/images/Cognito/ter6.png)
![up](/static/images/Cognito/tercl1.png)
![up](/static/images/Cognito/tercl2.png)

- Default sign in page
![up](/static/images/Cognito/re1.png)
- Default sign up page
![up](/static/images/Cognito/re2.png)
- Authentication of Cognito default IP (Identity provider)
![up](/static/images/Cognito/re3.png)
**After sign up and login in  -> turn off the tab -> open another tab or session -> your session is the same and you cannot sign up again.  -> your session has been captured.**
![up](/static/images/Cognito/tercl3.png)

**After success sign in you will be redict to "aws.training" page**
![up](/static/images/Cognito/url_callback.jpg)

### OpenID config
    https://cognito-idp.{region}.amazonaws.com/{userpoolid}/.well-known/openid-configuration
![up](/static/images/Cognito/openidconfig.jpg)
