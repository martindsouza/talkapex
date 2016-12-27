---
title: APEX_UTIL.JSON_FROM_SQL No Rows Bug + Fix
tags:
  - apex
date: 2009-05-26 09:00:00
alias:
---

I ran into an issue yesterday using APEX_UTIL.JSON_FROM_SQL with a query that returned no rows. This function is used in AJAX calls to return the results of a query as a JSON object.

To replicate this issue you can do the following in APEX:

<span style="font-weight: bold">1- Create an On Demand Application Process</span>

<pre class="brush: sql">
-- AP_JSON_TEST
DECLARE
  v_sql VARCHAR2 (4000);
BEGIN
  -- Note: This query is meant to return no rows
  v_sql := 'SELECT ename FROM emp WHERE 1 = 2';

  -- Print JSON result set
  apex_util.json_from_sql (v_sql);
END;
</pre>

<span style="font-weight: bold">2- Run the following JS code</span><span style="font-style:italic">(easiest using firebug)</span>
<pre class="brush: js">
  var get = new htmldb_Get(null,$v('pFlowId'),'APPLICATION_PROCESS=AP_TODO_DEL',$v('pFlowStepId'));  
  vReturn = get.get();
</pre>

At this point if you're using FireBug you'll notice that <span style="color:red">"sqlerrm:ORA-06502: PL/SQL: numeric or value error"</span> now appears in the console window.

At first glance it appears that you have something wrong with your Application Process or with your query. After some digging around I finally realized that it's a bug with APEX_UTIL.JSON_FROM_SQL.

The good news is that it's really easy to fix. All you need to do is catch any exceptions and return a JSON object with no rows:

<pre class="brush: sql">
-- AP_JSON_TEST

DECLARE
  v_sql VARCHAR2 (4000);
BEGIN
  -- Note: This query is meant to return no rows
  v_sql := 'SELECT ename FROM emp WHERE 1 = 2';
  -- Print JSON result set
  apex_util.json_from_sql (v_sql);

-- *** FIX ***  
EXCEPTION
  WHEN OTHERS THEN
    HTP.p ('{"row":[]}');
END;
</pre>
