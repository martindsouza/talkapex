---
title: APEX_ADMINISTRATOR_ROLE
tags:
  - apex
date: 2012-08-30 07:00:00
alias:
---

The [APEX Dictionary](http://www.talkapex.com/2008/11/how-to-list-apex-dictionary-views-using.html) is a set of views that describe all the different objects of an APEX application. They are extremely useful when trying to compare objects or using the metadata in your application. One example, which I recently wrote about, is to use a view from the dictionary to [leverage the APEX build options in your PL/SQL code](http://www.talkapex.com/2012/08/integrating-build-options-in-your-plsql.html).

By default the views will only allow you to see information for applications, and their objects, that are linked to your current schema (i.e. the application's parsing schema must be the same as your schema). For older versions of APEX the only way to view all the applications in the entire database was to either log in as SYSTEM or SYS.

In newer versions of APEX (I think it was released in APEX 4.1) there's a new database role called APEX_ADMINISTRATOR_ROLE. This role allows for non SYSTEM/SYS users to view all the APEX applications in your database. It's a very useful thing to have if you want to run your own scripts to check for things like standards, security audits, performance, etc.

One example where this role can be very useful is to monitor for slow running pages in all your applications across the entire database (rather than just ones in a particular schema). The following query, executed by a user that has the APEX_ADMINISTRATOR_ROLE, will show all the slow pages in the past two days:
<pre class="brush: sql; highlight: []">
SELECT *
FROM apex_workspace_activity_log
WHERE trunc(view_date) >= trunc(SYSDATE) - 1 -- Just look at the past 2 days
  AND elapsed_time > 1; -- 1 = 1 second
</pre>This is just one of many examples where the APEX_ADMINISTRATOR_ROLE can be
useful for system wide level analysis.

The APEX_ADMINISTRATOR_ROLE also allows you to run procedures in the [APEX_INSTANCE_ADMIN](http://docs.oracle.com/cd/E23903_01/doc/doc.41/e21676/apex_instance.htm#CACGJEDD) package.
