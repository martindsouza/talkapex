---
title: APEX Region Errors - Part 1
tags:
  - APEX
  - ORACLE APEX
date: 2010-10-18 09:00:00
alias:
---

A while ago I wrote about how to handle and customize the default unhandled exception error page in APEX: [http://www.talkapex.com/2010/09/custom-error-messages-in-apex.html](http://www.talkapex.com/2010/09/custom-error-messages-in-apex.html) The method described in the post catches page level errors and can notify administrators immediately when an undhandled exception occurs. 

Region level errors are not caught using the method above. An example of a region error is when you have a query that has divisor is equal to zero error. To simulate this error create a standard report region with the following query:
<pre class="brush: sql; highlight: 3">
SELECT *
  FROM emp
 WHERE 1 / 0 = 0 -- Causes an ORA-01476: divisor is equal to zero
</pre>
This query will return a "<span style="font-style:italic;">ORA-01476: divisor is equal to zero</span>" error message in APEX.

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TLuUB6biMtI/AAAAAAAAD0w/QPLuUQ-IlOE/s400/region_error.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TLuUB6biMtI/AAAAAAAAD0w/QPLuUQ-IlOE/s1600/region_error.jpg)
Starting with APEX 4.0 region level errors are logged in the APEX Activity Log. The following query identifies both page and region level errors:
<pre class="brush: sql;">
SELECT apex_user,
       application_id,
       application_name,
       page_id,
       page_name,
       view_date,
       apex_session_id,
       error_message,
       error_on_component_type,
       error_on_component_name -- Region/Process that caused the issue
  FROM apex_workspace_activity_log
 WHERE error_message IS NOT NULL
</pre>
I strongly recommend that you frequently scan the activity logs for errors. <span style="font-style:italic;">Part 2 of will describe a way to be automatically notified of region level errors.</span>

They're a few caveats that you should be aware of:

- As mentioned, the activity log identifies both region and page level errors. <span style="font-style:italic;">APEX_WORKSPACE_ACTIVITY_LOG.ERROR_ON_COMPONENT_NAME</span> identifies the region or process that the error occured in.

- When multiple region level errors occur only the first error is logged in the activity log.

- From my limited testing, errors in Interactive Reports are not logged. Their may be other types of regions that don't log errors as well. 

Given the caveats listed above querying the activity logs help identify some, but not all, errors that happen in your application.

[APEX Region Errors - Part 2](http://www.talkapex.com/2010/10/apex-region-errors-part-2.html)