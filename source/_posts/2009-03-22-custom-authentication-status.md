---
title: Custom Authentication Status
tags:
  - apex
date: 2009-03-22 10:27:00
alias:
---

When writing custom authentication scheme I noticed that invalid logins weren't showing up properly in the apex logs (apex_workspace_access_log).

Here's how to re-create the problem along with the fix.

First let's create a custom authentication function:
<pre class="brush: sql;">
CREATE OR REPLACE FUNCTION blog_cust_auth (
  p_username IN VARCHAR2,
  p_password IN VARCHAR2
)
  RETURN BOOLEAN
AS
BEGIN
  IF     LOWER (p_username) = 'pass'
     AND LOWER (p_password) = '123' THEN
    RETURN TRUE;
  ELSE
    RETURN FALSE;
  END IF;
END;
</pre>
Let's set the APEX authentication scheme to use this custom function:

Shared Components / Authentication Schemes:
- Create
- From Scratch
- Name: BLOG_TEST
- Keep hitting "Next" until:
  - Credentials Verification Method:
    - return blog_cust_auth
  - Keep hitting "Next" until the end

- Change the current authentication scheme to BLOG_TEST
  - On the Authentication Schemes page, on the right hand side select "Change Current" and select "BLOG_TEST'

Let's create a region to always display the authentication statuses:
- Create Page
  - Page Zero
- On Page 0 Create a region
  - Report
<pre class="brush: sql;">
SELECT   user_name,
         authentication_method,
         access_date,
         authentication_result,
         custom_status_text
    FROM apex_workspace_access_log
   WHERE application_id = :APP_ID
ORDER BY access_date DESC
</pre>
Now let's try to log in using a valid username and password:
- username: pass, password: 123

If things worked well you're now on P1, and the "AUTHENITCATION_RESULT" is flagged as "AUTH_SUCCESS"

[![](http://3.bp.blogspot.com/_33EF80fk9sM/ScZntp_wliI/AAAAAAAADnQ/PRaOCMBcJsc/s400/01_cust_auth_status.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/ScZntp_wliI/AAAAAAAADnQ/PRaOCMBcJsc/s1600-h/01_cust_auth_status.bmp)

Now logout and try to login with invalid credentials
- username: pass, password: fail

You'll notice that the login failed, however it still logged it as a successful login attempt:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/ScZn4N5ScYI/AAAAAAAADnY/T6KZdkpFqyE/s400/02_cust_auth_status.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/ScZn4N5ScYI/AAAAAAAADnY/T6KZdkpFqyE/s1600-h/02_cust_auth_status.bmp)

Let's update our authentication function to use the 2 authentication result customizations:
apex_util.set_authentication_result and apex_util.set_custom_auth_status
<pre class="brush: sql;">
CREATE OR REPLACE FUNCTION blog_cust_auth (
  p_username IN VARCHAR2,
  p_password IN VARCHAR2
)
  RETURN BOOLEAN
AS
BEGIN
  IF     LOWER (p_username) = 'pass'
     AND LOWER (p_password) = '123' THEN
    RETURN TRUE;
  ELSE
    -- Set APEX authentication Codes
    apex_util.set_authentication_result (p_code => 3);
    -- Set our own APEX custom text
    apex_util.set_custom_auth_status (p_status => 'Bad Username');
    RETURN FALSE;
  END IF;
END;
</pre>
Let's now see what happens when we now login with invalid credentials:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/ScZoFSUVHOI/AAAAAAAADng/rusi5Cp0TTc/s400/03_cust_auth_status.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/ScZoFSUVHOI/AAAAAAAADng/rusi5Cp0TTc/s1600-h/03_cust_auth_status.bmp)

Notice that we now have proper error information in the logs? Here's a list of some codes you can use for apex_util.set_authentication_result, taken from the view apex_workspace_access_log:

0, 'AUTH_SUCCESS',
1, 'AUTH_UNKNOWN_USER',
2, 'AUTH_ACCOUNT_LOCKED',
3, 'AUTH_ACCOUNT_EXPIRED',
4, 'AUTH_PASSWORD_INCORRECT',
5, 'AUTH_PASSWORD_FIRST_USE',
6, 'AUTH_ATTEMPTS_EXCEEDED',
7, 'AUTH_INTERNAL_ERROR',    

Of course you can use your own codes but I'd suggest sticking with these codes to utilize the APEX views. You can supplement the codes with the custom authentication status text (apex_util.set_custom_auth_status)
