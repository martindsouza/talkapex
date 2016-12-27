---
title: 'APEX 4.0: New Columns in APEX_WORKSPACE_ACTIVITY_LOG'
tags:
  - apex
date: 2010-12-08 08:00:00
alias:
---

Over a year ago I wrote about how to permanently store some of the log information from APEX: [http://www.talkapex.com/2009/05/apex-logs-storing-log-data.html](http://www.talkapex.com/2009/05/apex-logs-storing-log-data.html) APEX 4.0 has some additional columns in APEX_WORKSPACE_ACTIVITY_LOG which you may want to store as well. The following query identifies the new columns:

<span style="font-style:italic;">Note: This query assumes you still have an old instance of APEX 3.2 and a new instance of APEX 4.0 on your database</span><pre class="brush: sql">SELECT column_name
  FROM all_tab_columns
 WHERE owner = 'APEX_040000'
   AND table_name = 'APEX_WORKSPACE_ACTIVITY_LOG'
MINUS
SELECT column_name
  FROM all_tab_columns
 WHERE owner = 'APEX_030200'
   AND table_name = 'APEX_WORKSPACE_ACTIVITY_LOG'
ORDER BY column_name</pre>In case you don't have an old 3.2 instance on your database the new columns are:

CONTENT_LENGTH
INTERACTIVE_REPORT_ID
IR_SAVED_REPORT_ID
IR_SEARCH
WS_APPLICATION_ID
WS_DATAGRID_ID
WS_PAGE_ID

If you permanently store the log information you may want to update your tables and scripts to store the new information.
