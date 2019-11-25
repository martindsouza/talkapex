---
title: APEX Report with checkboxes (advanced).
tags:
  - apex
  - checkbox
date: 2009-01-20 23:58:00
alias:
---

**<span style="color: red;">Note:</span> This solution was updated to account for APEX's new features and APIs. See the updated post here:&nbsp;[http://www.talkapex.com/2015/09/report-with-checkboxes-update.html](http://www.talkapex.com/2015/09/report-with-checkboxes-update.html)**

A colleague of mine wanted to build a report with check boxes. Once the user selected all the rows, they would click submit and the application would process the rows. Sounds pretty simple and straight forward however he did have some extra requirements:

- The report is an interactive report
- There may be up to 10,000 records in the report
- When the user "scrolls" through the report (i.e. uses pagination), if they checked off a box it should remain checked the entire time (i.e. if they check an row in the 1st 15 rows, then view rows 16~30, then go back to rows 1~15 it should remain checked)

So here's the example on how to do it. The working example can be found here: [http://apex.oracle.com/pls/otn/f?p=20195:500](http://apex.oracle.com/pls/otn/f?p=20195:500)

Create an application item F_EMPNO_LIST. Note you can use a page item as well...

Setup IR:
<pre class="brush: sql;">
SELECT apex_item.checkbox (1,
                           empno,
                           'onchange="spCheckChange(this);"',
                           :f_empno_list,
                           ':'
                          ) checkbox,
       empno, ename, job
  FROM emp</pre>
Once the report is setup set the row display to 5 (you'll need it for this example)

- Add an HTML region and add the following code: (Note: the jQuery call is not needed... for now)
<pre class="brush: js;">
<script src="http://www.google.com/jsapi"></script>
<script>
  // Load jQuery
  google.load("jquery", "1.2.6", {uncompressed:true});

  function spCheckChange(pThis){
    var get = new htmldb_Get(null,$v('pFlowId'),'APPLICATION_PROCESS=CHECKBOX_CHANGE',$v('pFlowStepId'));  
    get.addParam('x01',pThis.value); //Value that was checked
    get.addParam('x02',pThis.checked ? 'Y':'N'); // Checked Flag
    gReturn = get.get();

    $x('checkListDisp').innerHTML=gReturn;
  }

</script>
CHECKBOX List:

<div id="checkListDisp">
&amp;F_EMPNO_LIST.</div>
</pre>
Now create an application process (on Demand) called: CHECKBOX_CHANGE
<pre class="brush: sql;">
-- Application Process: CHECKBOX_CHANGE
-- On Demand...

DECLARE
  v_item_val NUMBER := apex_application.g_x01;
  v_checked_flag VARCHAR2 (1) := apex_application.g_x02;
BEGIN
  IF v_checked_flag = 'Y' THEN
    -- Add to the list
    IF :f_empno_list IS NULL THEN
      :f_empno_list := ':' || v_item_val || ':';
    ELSE
      :f_empno_list := :f_empno_list || v_item_val || ':';
    END IF;
  ELSE
    -- Remove from the list
    :f_empno_list := REPLACE (:f_empno_list, ':' || v_item_val || ':', ':');
  END IF;

  -- Just for testing
  HTP.p (:f_empno_list);
END;</pre>
- On the post page create a query to view data (you can process how you need/want)
<pre class="brush: sql;">
select *
from emp
where instr(:F_EMPNO_LIST, ':' || empno || ':') &gt; 0</pre>
Please note there may be better/faster ways to implement the SQL code etc.

Here are some links on the x01 code that I used:
[http://carlback.blogspot.com/2008/03/new-stuff-2-x01-and-friends.html](http://carlback.blogspot.com/2008/03/new-stuff-2-x01-and-friends.html) and [http://carlback.blogspot.com/2008/04/new-stuff-q.html](http://carlback.blogspot.com/2008/04/new-stuff-q.html)
