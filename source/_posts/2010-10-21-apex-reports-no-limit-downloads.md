---
title: 'APEX Reports: No Limit Downloads'
tags:
  - APEX
  - ORACLE APEX
date: 2010-10-21 09:00:00
alias:
---

When configuring a standard report in APEX you can define the maximum number of rows that the query will return. This setting is called Max Row Count and can be configured in the Report Attributes tab. If you leave it blank it will default to 500 rows.

[![](http://4.bp.blogspot.com/_33EF80fk9sM/TMBHWaM0ypI/AAAAAAAAD1M/qQI-dhYYyuM/s400/max_row_count.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/TMBHWaM0ypI/AAAAAAAAD1M/qQI-dhYYyuM/s1600/max_row_count.jpg)
It is recommended that you set the Max Row Count with a low value. Using a small number will help improve performance since it reduces the amount rows APEX must count in order to display the pagination information. On a business perspective, it may not make sense to return thousands of rows for a user to view online.

What if the user wants to retrieve all the data when they download the report? From the end users perspective this makes sense since they may want to extract all the data to do some custom analysis.

My first thought was to use a page item for the Maximum Row Count. I quickly discovered that it only takes a numeric value. As a work around you can control the rows returned directly from the SQL statement. Here's an example of this:

<span style="font-weight:bold;">- Create a large table:</span>
<pre class="brush: sql">
CREATE TABLE large_emp
AS
      SELECT *
        FROM emp
  CONNECT BY LEVEL <= 5;</pre><span style="font-weight:bold;">- Create standard report</span>
Create a standard report using the following query. Set the Max Row Count to 999999999 (i.e. some very large number)
<pre class="brush: sql; highlight: 6;">
SELECT ename,
       job,
       sal,
       comm
  FROM large_emp
 WHERE ROWNUM <= CASE WHEN :request LIKE ('FLOW_EXCEL_OUTPUT%') THEN ROWNUM ELSE 500 END;</pre>You are manually defining the Max Row Count by adding in ROWNUM predicate.

When users <span style="font-weight:bold;">views</span> the report in APEX it will only display a maximum of 500 rows. When they <span style="font-weight:bold;">download</span> the report the csv file will contain all the rows.

A caveat with this approach is that if your report has more than 500 rows you won't see the "<span style="font-style:italic;">... of more than 500</span>" rows message as part of the pagination. Instead users will see "<span style="font-style:italic;">rows ... of 500</span>" in the pagination. Users may be misled to think that the report only has 500 rows of data.

When I reviewed this technique I was concerned about performance issues when viewing the report in APEX. Here are the TKPROF outputs of the original query (with Max Row Count = 500) and the alternate query with the ROWNUM predicate (Max Row Count = 999999999):

<pre class="brush: sql; highlight: 42;">
-- Both outputs are for VIEWING the report (not downloading it)
--
-- Original Query
-- Max Row Count = 500
-- Run for displaying rows 1~15
SELECT ename, job, sal, comm
  FROM large_emp
 order by 3 -- Automatically added by APEX

call     count       cpu    elapsed       disk      query    current        rows
------- ------  -------- ---------- ---------- ---------- ----------  ----------
Parse        1      0.00       0.00          0          1          0           0
Execute      2      0.00       0.00          0          0          0           0
Fetch      501      0.65       1.42         14       3417          3         501
------- ------  -------- ---------- ---------- ---------- ----------  ----------
total      504      0.65       1.42         14       3418          3         501

Misses in library cache during parse: 1
Optimizer mode: ALL_ROWS
Parsing user id: 40     (recursive depth: 2)

Rows     Row Source Operation
-------  ---------------------------------------------------
    501  SORT ORDER BY (cr=3417 pr=14 pw=2053 time=1417580 us)
 579194   TABLE ACCESS FULL LARGE_EMP (cr=3417 pr=0 pw=0 time=1158549 us)

Elapsed times include waiting on following events:
  Event waited on                             Times   Max. Wait  Total Waited
  ----------------------------------------   Waited  ----------  ------------
  direct path write temp                         70        0.00          0.00
  direct path read temp                           2        0.00          0.00

-- *************************************

-- New Query
-- Max Row Count = 9999999999
-- Run for displaying rows 1~15
SELECT ename, job, sal, comm
  FROM large_emp
 WHERE ROWNUM <= CASE WHEN :request LIKE ('FLOW_EXCEL_OUTPUT%') THEN ROWNUM ELSE 500 END
 order by 3 -- Automatically added by APEX

call     count       cpu    elapsed       disk      query    current        rows
------- ------  -------- ---------- ---------- ---------- ----------  ----------
Parse        2      0.00       0.00          0          0          0           0
Execute      2      0.00       0.00          0          0          1           0
Fetch      501      0.38       0.53          0       3417          0         500
------- ------  -------- ---------- ---------- ---------- ----------  ----------
total      505      0.38       0.54          0       3417          1         500

Misses in library cache during parse: 1
Parsing user id: 40     (recursive depth: 2)

Rows     Row Source Operation
-------  ---------------------------------------------------
    500  SORT ORDER BY (cr=3417 pr=0 pw=0 time=535125 us)
    500   COUNT  (cr=3417 pr=0 pw=0 time=3376 us)
    500    FILTER  (cr=3417 pr=0 pw=0 time=2298 us)
 579194     TABLE ACCESS FULL LARGE_EMP (cr=3417 pr=0 pw=0 time=5212959 us)</pre>I'm not a performance guru and I can't comment too much on these outputs. If you think this will have negative effects on performance please add your feedback as a comment on this post.