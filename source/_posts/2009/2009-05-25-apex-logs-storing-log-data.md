---
title: 'APEX Logs: Storing Log Data'
tags:
  - apex
date: 2009-05-25 09:00:00
alias:
---

For those of you that use the APEX Logs you may not be aware that they store at best 4 weeks of data and at worst 2 weeks of data. They're actually 2 log tables, each one gets purged roughly every 2 weeks. For those of you who don't know about or use the APEX logs I suggest you read up on this.

You can get a list of the APEX logs by running the following query:

<pre class="brush: sql">
SELECT *
  FROM apex_dictionary
 WHERE column_id = 0
   AND apex_view_name LIKE '%LOG%'
</pre>

I strongly recommend that you explicitly store the log data into your own tables. I've encountered several instances where the APEX Logs have helped get me out of some sticky situations. It can also help you get some usage stats and page stats.

Here's how to keep a copy of the APEX log tables:

<span style="font-style:italic; color:red">Note: You'll need to run this in each of your schemas that you have APEX applications in since the APEX Log Views only display application information who's parsing schema matches the current Oracle user</span>

<span style="font-weight:bold;">1- Create the APEX log tables</span>
<pre class="brush: sql">
-- Login information
CREATE TABLE tapex_workspace_access_log
AS SELECT * FROM apex_workspace_access_log;

-- Page access information
CREATE TABLE tapex_workspace_activity_log
AS SELECT * FROM apex_workspace_activity_log;
</pre>

<span style="font-weight:bold;">2- Update the APEX log tables</span>

<span style="font-style:italic;">Note: You may want to store this in a procedure and run as a nightly scheduled job so you don't forget to update the tables</span>

<pre class="brush: sql">
INSERT INTO tapex_workspace_access_log
            (workspace, application_id, application_name, user_name, authentication_method, application_schema_owner,
             access_date, ip_address, authentication_result, custom_status_text, workspace_id)
  SELECT alog.workspace,
         alog.application_id,
         alog.application_name,
         alog.user_name,
         alog.authentication_method,
         alog.application_schema_owner,
         alog.access_date,
         alog.ip_address,
         alog.authentication_result,
         alog.custom_status_text,
         alog.workspace_id
    FROM apex_workspace_access_log alog,
         tapex_workspace_access_log x
   WHERE alog.access_date = x.access_date(+)
     AND x.ROWID IS NULL
     AND alog.application_schema_owner = USER;

INSERT INTO tapex_workspace_activity_log
            (workspace, apex_user, application_id, application_name, application_schema_owner, page_id, page_name,
             view_date, think_time, log_context, elapsed_time, rows_queried, ip_address, AGENT, apex_session_id,
             error_message, error_on_component_type, error_on_component_name, page_view_mode, regions_from_cache,
             workspace_id)
  SELECT alog.workspace,
         alog.apex_user,
         alog.application_id,
         alog.application_name,
         alog.application_schema_owner,
         alog.page_id,
         alog.page_name,
         alog.view_date,
         alog.think_time,
         alog.log_context,
         alog.elapsed_time,
         alog.rows_queried,
         alog.ip_address,
         alog.AGENT,
         alog.apex_session_id,
         alog.error_message,
         alog.error_on_component_type,
         alog.error_on_component_name,
         alog.page_view_mode,
         alog.regions_from_cache,
         alog.workspace_id
    FROM apex_workspace_activity_log alog,
         tapex_workspace_activity_log x
   WHERE alog.view_date = x.view_date(+)
     AND alog.apex_session_id = x.apex_session_id(+)
     AND alog.application_schema_owner = USER
     AND x.ROWID IS NULL;
</pre>
