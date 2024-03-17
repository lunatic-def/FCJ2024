---
title : "Result"
date :  "`r Sys.Date()`" 
weight : 2
---
***3. Result***

**1. User pools**
**Using the cognito default authenticate behavior, in the next lab i will try to do with other identity provider**
![up](/images/Cognito/ter1.png)
- Sign-in experience
![up](/images/Cognito/ter2.png)
- Sign-up experience:
![up](/images/Cognito/ter3.png)
- Messaging 
![up](/images/Cognito/ter4.png)
- App integration
![up](/images/Cognito/ter5.png)

**2. Client pools**
**NOTE** there is s client secret but in this picture i turn it off, you should config cs.
![up](/images/Cognito/ter6.png)
![up](/images/Cognito/tercl1.png)
![up](/images/Cognito/tercl2.png)

- Default sign in page
![up](/images/Cognito/re1.png)
- Default sign up page
![up](/images/Cognito/re2.png)
- Authentication of Cognito default IP (Identity provider)
![up](/images/Cognito/re3.png)
**After sign up and login in  -> turn off the tab -> open another tab or session -> your session is the same and you cannot sign up again.  -> your session has been captured.**
![up](/images/Cognito/tercl3.png)

**After success sign in you will be redict to "aws.training" page**
![up](/images/Cognito/url_callback.jpg)

### OpenID config
    https://cognito-idp.{region}.amazonaws.com/{userpoolid}/.well-known/openid-configuration
![up](/images/Cognito/openidconfig.jpg)
