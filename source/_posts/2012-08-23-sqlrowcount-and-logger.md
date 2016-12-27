---
title: SQL%ROWCOUNT and Logger
tags:
  - ORACLE
  - PL/SQL
date: 2012-08-23 07:00:00
alias:
---

[Logger](http://tylermuth.wordpress.com/2009/11/03/logger-a-plsql-logging-and-debugging-utility/) (by [Tyler Muth](http://tylermuth.wordpress.com/)) is a free open source logging tool for PL/SQL. Tyler initially launched it in 2009 and now it has become a staple tool for many Oracle development teams. If you've never heard of it go check it out.

When using logger I like to log the number of rows an update statement made after the update was performed. In order to do that I use the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SQL%ROWCOUNT</span> variable. The thing to be aware of is if you log <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SQL%ROWCOUNT </span>using logger it will be "reset" by the implicit insert that logger does.&nbsp; In the example below you'll notice that after calling logger the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SQL%ROWCOUNT</span> now has a value of 0 since it does an insert:
<pre class="brush: sql; highlight: [13]">
SQL> BEGIN
  2    UPDATE emp
  3    set sal = sal;
  4
  5    dbms_output.put_line('Rows updated: ' || SQL%rowcount);
  6    dbms_output.put_line('Rows updated: ' || SQL%rowcount);
  7    logger.log('Rows updated: ' || SQL%rowcount);
  8    dbms_output.put_line('Rows updated: ' || SQL%rowcount);
  9  END;
 10  /
Rows updated: 14
Rows updated: 14
Rows updated: 0

PL/SQL procedure successfully completed.

SQL> SELECT text
  2  FROM logger_logs
  3  WHERE ROWNUM = 1
  4  ORDER BY ID DESC;

TEXT
------------------
Rows updated: 14
</pre>This is important to know because sometimes you may do some additional work after an update statement depending on how many records were updated in the previous statement. You should change your code from:
<pre class="brush: sql; highlight: []">
UPDATE emp
SET sal = sal;
logger.log('Rows updated: ' || SQL%rowcount);

IF SQL%rowcount > 0 THEN
  ...
END IF;
</pre>To:
<pre class="brush: sql; highlight: [3,4,6]">
UPDATE emp
SET sal = sal;
l_row_count := SQL%rowcount;
logger.LOG('Rows updated: ' || l_row_count);

IF l_row_count > 0 THEN
  ...
END IF;
</pre>