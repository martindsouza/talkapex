---
title: Referencing USER in APEX
tags:
  - apex
date: 2014-03-05 07:00:00
alias:
---

It’s not uncommon to reference the current user as `USER` in your PL/SQL code. A simple use case may be to determine the client or environment that you’re running in (ex: dev, test, prod).

Referencing `USER` will have some slight side effects when running the code in APEX as the current `USER` is actually `APEX_PUBLIC_USER` (or what ever user you configured). This can cause issues in your application. To resolve it, simply reference `sys_context('userenv','current_schema’)` instead.

Example:
```sql
-- Via SQL*Plus
select user, sys_context('userenv','current_schema') sc_user
from dual;

USER       SC_USER
---------- --------------------
GIFFY      GIFFY
```

If you run the same query in APEX the output is shown below. You'll notice that referencing `USER` it does not display my current schema (`GIFFY` in this case).
<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-ozCvnbjIbkA/UwqzQGMCjSI/AAAAAAAAEnE/C2g_N5lmlVo/s1600/apex_users.png)](http://2.bp.blogspot.com/-ozCvnbjIbkA/UwqzQGMCjSI/AAAAAAAAEnE/C2g_N5lmlVo/s1600/apex_users.png)</div>The same applies to compiled code executed from APEX. For example if you have a procedure that references USER and that procedure is run from APEX then `USER` will be `APEX_PUBLIC_USER`.

This can be really tough to detect in automated tests as when testing via SQL*Plus, `USER` will return the current schema name.
