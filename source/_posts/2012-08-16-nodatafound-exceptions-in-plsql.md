---
title: NO_DATA_FOUND Exceptions in PL/SQL Functions Called in Queries
tags:
  - PL/SQL
date: 2012-08-16 07:00:00
alias:
---

Functions in PL/SQL can be a tricky thing as they don't always raise an exception when one occurs. You may be asking yourself: _How, I thought functions and procedures always raise exceptions when one occurs_? This is true except in some very specific situations.

First lets look at a very simple function which is then executed in both PL/SQL and in a query. It should (and does) raise an exception in both cases since we have a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">1/0</span> in it.
<pre class="brush: sql;">SQL> CREATE OR REPLACE FUNCTION f_generate_error RETURN VARCHAR2
  2  AS
  3    l_x pls_integer;
  4  BEGIN
  5    SELECT 1 INTO l_x FROM dual WHERE 1/0 = 0;
  6    RETURN 'No exception raised';
  7  END;
  8  /

Function created.

SQL> EXEC dbms_output.put_line(f_generate_error);
BEGIN dbms_output.put_line(f_generate_error); END;

*
ERROR at line 1:
ORA-01476: divisor is equal to zero
ORA-06512: at "MARTIN.F_GENERATE_ERROR", line 5
ORA-06512: at line 1

SQL> SELECT f_generate_error FROM dual;
SELECT f_generate_error FROM dual
       *
ERROR at line 1:
ORA-01476: divisor is equal to zero
ORA-06512: at "MARTIN.F_GENERATE_ERROR", line 5
</pre>What if we change the query slightly so that the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SELECT INTO</span> statement doesn't return any rows (by applying a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">1=0</span> predicate) and raises a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">NO_DATA_FOUND</span> exception?
<pre class="brush: sql; highlight: [22,23,24,25,26,27]">SQL> CREATE OR REPLACE FUNCTION f_generate_error RETURN VARCHAR2
  2  AS
  3    l_x pls_integer;
  4  BEGIN
  5    SELECT 1 INTO l_x FROM dual WHERE 1=0;
  6    RETURN 'No exception raised';
  7  END;
  8  /

Function created.

SQL> EXEC dbms_output.put_line(f_generate_error);
BEGIN dbms_output.put_line(f_generate_error); END;

*
ERROR at line 1:
ORA-01403: no data found
ORA-06512: at "MARTIN.F_GENERATE_ERROR", line 5
ORA-06512: at line 1

SQL> SELECT f_generate_error FROM dual;

F_GENERATE_ERROR
----------------------------------------------------------------

1 row selected.</pre>When we run it in PL/SQL it raises an exception but when we run it in a query it doesn't. It just returns a null value which doesn't really tell the calling query that an exception was raised. This can obviously cause issues and unexpected behavior in your application. 

According to the [Oracle documentation](http://docs.oracle.com/cd/B13789_01/appdev.101/b10807/07_errs.htm) the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">NO_DATA_FOUND</span> exception will not propagate the exception if run in a query: "_Because this exception is used internally by some SQL functions to signal that they are finished, you should not rely on this exception being propagated if you raise it within a function that is called as part of a query._"

To get around this issue you can raise a custom exception when you encounter a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">NO_DATA_FOUND</span> exception in functions so that it will propagate when called via a SQL query. In the example below a custom exception is raised and the exception is propagated when called from a query.
<pre class="brush: sql; highlight: [7,8,9]">SQL> CREATE OR REPLACE FUNCTION f_generate_error RETURN VARCHAR2
  2  AS
  3    l_x pls_integer;
  4  BEGIN
  5    SELECT 1 INTO l_x FROM dual WHERE 1=0;
  6    RETURN 'No exception raised';
  7  EXCEPTION
  8    WHEN NO_DATA_FOUND THEN
  9      raise_application_error(-20001, 'Custom NO_DATA_FOUND');
 10  END;
 11  /

Function created.

SQL> SELECT f_generate_error FROM dual;
SELECT f_generate_error FROM dual
       *
ERROR at line 1:
ORA-20001: Custom NO_DATA_FOUND
ORA-06512: at "MARTIN.F_GENERATE_ERROR", line 9
</pre>