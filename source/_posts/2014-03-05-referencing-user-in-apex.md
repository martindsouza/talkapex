---
title: Referencing USER in APEX
tags:
  - apex
date: 2014-03-05 07:00:00
alias:
---

It’s not uncommon to reference the current user as <span style="font-family: Courier New, Courier, monospace;">USER</span> in your pl/sql code. A simple use case may be to determine the client or environment that you’re running in (ex: dev, test, prod).

Referencing <span style="font-family: Courier New, Courier, monospace;">USER</span> will have some slight side effects when running the code in APEX as the current <span style="font-family: Courier New, Courier, monospace;">USER</span> is actually <span style="font-family: Courier New, Courier, monospace;">APEX_PUBLIC_USER</span> (or what ever user you configured). This can cause issues in your application. To resolve it, simply reference <span style="font-family: Courier New, Courier, monospace;">sys_context('userenv','current_schema’)</span> instead.

Example:
<pre class="brush: sql;">-- Via SQL*Plus
select user, sys_context('userenv','current_schema')
from dual;

USER        SYS_CONTEXT('USERENV
---------- --------------------
GIFFY        GIFFY
</pre>If you run the same query in APEX the output is shown below. You'll notice that referencing <span style="font-family: Courier New, Courier, monospace;">USER</span> it does not display my current schema (GIFFY in this case).
<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-ozCvnbjIbkA/UwqzQGMCjSI/AAAAAAAAEnE/C2g_N5lmlVo/s1600/apex_users.png)](http://2.bp.blogspot.com/-ozCvnbjIbkA/UwqzQGMCjSI/AAAAAAAAEnE/C2g_N5lmlVo/s1600/apex_users.png)</div>The same applies to compiled code executed from APEX. For example if you have a procedure that references USER and that procedure is run from APEX then <span style="font-family: Courier New, Courier, monospace;">USER</span> will be <span style="font-family: Courier New, Courier, monospace;">APEX_PUBLIC_USER</span>.

This can be really tough to detect in automated tests as when testing via SQL*Plus, <span style="font-family: Courier New, Courier, monospace;">USER</span> will return the current schema name.
