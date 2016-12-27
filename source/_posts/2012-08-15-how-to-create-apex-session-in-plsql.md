---
title: How to Create an APEX Session in PL/SQL
tags:
  - APEX
date: 2012-08-15 08:38:00
alias:
---

Debugging a function or procedure which references APEX items using the V/NV functions or trying to debug APEX collections can be frustrating when the only way to set, and view, the values is to actually do it through the application. Thankfully, there's a way to create an APEX session from a PL/SQL session to test out your code.

They're various examples on the [APEX forums](https://forums.oracle.com/forums/forum.jspa?forumID=137) on how to create an APEX session in PL/SQL. Here's one version of it.

<pre class="brush: sql;">CREATE OR REPLACE PROCEDURE sp_create_apex_session(
  p_app_id IN apex_applications.application_id%TYPE,
  p_app_user IN apex_workspace_activity_log.apex_user%TYPE,
  p_app_page_id IN apex_application_pages.page_id%TYPE DEFAULT 1) 
AS
  l_workspace_id apex_applications.workspace_id%TYPE;
  l_cgivar_name  owa.vc_arr;
  l_cgivar_val   owa.vc_arr;
BEGIN

  htp.init; 

  l_cgivar_name(1) := 'REQUEST_PROTOCOL';
  l_cgivar_val(1) := 'HTTP';

  owa.init_cgi_env( 
    num_params => 1, 
    param_name => l_cgivar_name, 
    param_val => l_cgivar_val ); 

  SELECT workspace_id
  INTO l_workspace_id
  FROM apex_applications
  WHERE application_id = p_app_id;

  wwv_flow_api.set_security_group_id(l_workspace_id); 

  apex_application.g_instance := 1; 
  apex_application.g_flow_id := p_app_id; 
  apex_application.g_flow_step_id := p_app_page_id; 

  apex_custom_auth.post_login( 
    p_uname => p_app_user, 
    p_session_id => null, -- could use APEX_CUSTOM_AUTH.GET_NEXT_SESSION_ID
    p_app_page => apex_application.g_flow_id||':'||p_app_page_id); 
END;</pre>To create an APEX session (in PL/SQL) to mimic some tests I can do the following:  
<pre class="brush: sql;">SQL> BEGIN
  2    sp_create_apex_session(
  3      p_app_id => 106,
  4      p_app_user => 'MARTIN',
  5      p_app_page_id => 10);
  6  END;
  7  /

PL/SQL procedure successfully completed.
</pre>View some APEX session state variables: 
<pre class="brush: sql;">SQL> SELECT v('APP_USER') app_user, v('APP_SESSION') app_session,
  2    v('APP_PAGE_ID') page_id, v('P1_X') p1_x
  3  FROM dual;

APP_USER   APP_SESSION     PAGE_ID    P1_X
---------- --------------- ---------- ----------
MARTIN     374363229560201 10

SQL> -- Set P1_X
SQL> exec apex_util.set_session_state('P1_X', 'abc');

PL/SQL procedure successfully completed.

SQL> SELECT v('APP_USER') app_user, v('APP_SESSION') app_session,
  2    v('APP_PAGE_ID') page_id, v('P1_X') p1_x
  3  FROM dual;

APP_USER   APP_SESSION     PAGE_ID    P1_X
---------- --------------- ---------- ----------
MARTIN     374363229560201 10         abc

SQL> -- Clear APEX Session State
SQL> exec APEX_UTIL.CLEAR_APP_CACHE(p_app_id => 106);

PL/SQL procedure successfully completed.</pre>You can also create and view collections using the APEX_COLLECTION APIs and the APEX_COLLECTION view.