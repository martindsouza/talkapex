---
title: How to Disable an APEX Application During a Release
tags:
  - apex
date: 2019-02-27 20:42:09
alias:
---


All my releases are done via scripts. In other words I don't deploy APEX applications from the browser, rather straight from [SQLcl](https://www.oracle.com/ca-en/database/technologies/appdev/sqlcl.html). Since some of my releases can take a while and puts the code in an unstable state I usually disable the application for the duration of the release. This article will show how to disable an APEX application in PL/SQL.

You can manually disable an application via `Shared Components > Application Definition > Availability > Status`:

{% asset_img status-options.png %}

When setting the `Status` to `Unavailable` it will look like this for users:

{% asset_img disabled-app.png %}

Note: they're various status options which allow you to determine how to define the status message.

Going back to releases, the overall order things occur in are:

1. Disable APEX application
1. Run release (DDL, DML, etc)
1. Update APEX application (this will clear the disabled setting that was manually done in the first step.

PL/SQL code to disable an APEX application:

```sql
declare
  l_app_id apex_applications.application_id%type := 102;
begin
  -- Pre APEX 18.x
  -- oos_util_apex.create_session(
  --   p_app_id => l_app_id,
  --   p_user_name => 'RELEASE'
  -- );

  -- APEX 18.x +
  apex_session.create_session (
    p_app_id => l_app_id,
    p_page_id => 1,
    p_username => 'RELEASE');

  apex_util.set_application_status(
    p_application_id => l_app_id,
    p_application_status => 'UNAVAILABLE',
    p_unavailable_value => 'Updating application');
  
  commit;
end;
/

```

If using a version lower than APEX 18.x you'll need to install [OOS Utils](https://github.com/OraOpenSource/oos-utils) to create an APEX session in SQL. 


Before running the above code you'll need to enable the runtime API by going to `Shared Components > Security Attributes > Database Session > Runtime API Usage` and check `Modify This Application`.

{% asset_img runtime-api.png %}

Documention for [`apex_util.set_application_status`](https://docs.oracle.com/database/apex-18.1/AEAPI/SET_APPLICATION_STATUS-Procedure.htm#AEAPI-GUID-59E0FE99-8911-41B3-823A-36D4C705ACA4).