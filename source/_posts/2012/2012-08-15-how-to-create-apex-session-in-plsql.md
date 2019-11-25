---
title: How to Create an APEX Session in PL/SQL
tags:
  - apex
  - plsql
date: 2012-08-15 08:38:00
alias:
---

Debugging a function or procedure which references APEX items using the V/NV functions or trying to debug APEX collections can be frustrating when the only way to set, and view, the values is to actually do it through the application. Thankfully, there's a way to create an APEX session from a PL/SQL session to test out your code.

They're various examples on the [APEX forums](https://forums.oracle.com/forums/forum.jspa?forumID=137) on how to create an APEX session in PL/SQL. Here's one version of it.

```sql
create or replace procedure sp_create_apex_session(
  p_app_id in apex_applications.application_id%type,
  p_app_user in apex_workspace_activity_log.apex_user%type,
  p_app_page_id in apex_application_pages.page_id%type default 1)
as
  l_workspace_id apex_applications.workspace_id%type;
  l_cgivar_name  owa.vc_arr;
  l_cgivar_val   owa.vc_arr;
begin

  htp.init;

  l_cgivar_name(1) := 'REQUEST_PROTOCOL';
  l_cgivar_val(1) := 'HTTP';

  owa.init_cgi_env(
    num_params => 1,
    param_name => l_cgivar_name,
    param_val => l_cgivar_val );

  select workspace_id
  into l_workspace_id
  from apex_applications
  where application_id = p_app_id;

  wwv_flow_api.set_security_group_id(l_workspace_id);

  apex_application.g_instance := 1;
  apex_application.g_flow_id := p_app_id;
  apex_application.g_flow_step_id := p_app_page_id;

  apex_custom_auth.post_login(
    p_uname => p_app_user,
    p_session_id => null, -- could use APEX_CUSTOM_AUTH.GET_NEXT_SESSION_ID
    p_app_page => apex_application.g_flow_id||':'||p_app_page_id);
end;
```

To create an APEX session (in PL/SQL) to mimic some tests I can do the following:  

```sql
begin
  sp_create_apex_session(
    p_app_id => 106,
    p_app_user => 'MARTIN',
    p_app_page_id => 10);
end;
/

PL/SQL procedure successfully completed.
```

View some APEX session state variables:

```sql
select 
  v('APP_USER') app_user, 
  v('APP_SESSION') app_session,
  v('APP_PAGE_ID') page_id, v('P1_X') p1_x
from dual;

APP_USER   APP_SESSION     PAGE_ID    P1_X
---------- --------------- ---------- ----------
MARTIN     374363229560201 10

-- Set P1_X
exec apex_util.set_session_state('P1_X', 'abc');

PL/SQL procedure successfully completed.

select
  v('APP_USER') app_user,
  v('APP_SESSION') app_session,
  v('APP_PAGE_ID') page_id,
  v('P1_X') p1_x
from dual;

APP_USER   APP_SESSION     PAGE_ID    P1_X
---------- --------------- ---------- ----------
MARTIN     374363229560201 10         abc

-- Clear APEX Session State
exec apex_util.clear_app_cache(p_app_id => 106);

PL/SQL procedure successfully completed.
```

You can also create and view collections using the `APEX_COLLECTION` APIs and the `APEX_COLLECTION` view.
