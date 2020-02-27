---
title: Changing the Maximum Session Idle Time in APEX
tags:
  - apex
date: 2020-02-16 06:44:16
alias:
---

APEX allows you to set the maximum idle time for a given session. This is an application level attribute that is set in `Shared Components > Security Attributes`. Under the `Session Management` there is a setting for `Maximum Session Idle Time in Seconds` which defaults to `3600` seconds:

{% asset_img max-idle-setting.png %}

When a new session is created in APEX, the max idle time is "copied" from the application settings directly into the user's session settings. If you change the setting at an application level **only new sessions will use the new value**. **Existing sessions will use the old value**.

The following example demonstrates when changes take effect. The current max idle time is set to `3600` and one session is active (i.e. one user is logged in):

```sql
-- App setting
select aa.maximum_session_idle_seconds
from apex_applications aa
where aa.application_id = 101
;

   MAXIMUM_SESSION_IDLE_SECONDS
_______________________________
                           3600

-- Active sessions
select
  ws.apex_session_id,
  ws.session_max_idle_sec
from apex_workspace_sessions ws
;

   APEX_SESSION_ID    SESSION_MAX_IDLE_SEC
__________________ _______________________
    10974491127217                    3600
```

The `Maximum Session Idle Time in Seconds` setting is is changed to `200` seconds and then a new user logs in (i.e. a new session is created):

```sql
-- App setting
select aa.maximum_session_idle_seconds
from apex_applications aa
where aa.application_id = 101
;

   MAXIMUM_SESSION_IDLE_SECONDS
_______________________________
                            200

-- Active sessions
select
  ws.apex_session_id,
  ws.session_max_idle_sec
from apex_workspace_sessions ws
;

   APEX_SESSION_ID    SESSION_MAX_IDLE_SEC
__________________ _______________________
     3565745405186                     200
    10974491127217                    3600
```

You can see that the older session (`10974491127217`) still retains the old max idle time (`3600` seconds) whereas the new session (`3565745405186`) uses the new value (`200` seconds).

When testing these settings it's important that you create a new session each time a change is made.