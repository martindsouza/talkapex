---
title: 'q Function: Escape Single Quotes'
tags:
  - APEX
date: 2009-03-16 07:21:00
alias:
---

Last year [Scott Spendolini](http://spendolini.blogspot.com/) gave our company an excellent 3 day training session on APEX. He also showed us a very nifty function (new in 10g I think) to help not having to escape single quotes when defining strings. This is not APEX specific, however it will help you if you ever have to write a block of PL/SQL to return an SQL statement for reports.

Instead of writing out a long description here's an example:
<pre class="brush: sql;">DECLARE
  v_sql VARCHAR2 (255);
  v_result VARCHAR2 (255);
BEGIN
  v_sql := 'select ''hello'' into :a from dual';

  EXECUTE IMMEDIATE v_sql
               INTO v_result;

  DBMS_OUTPUT.put_line (v_result);
END;</pre>
Notice how I had to put 2 single quotes around "Hello" to escape the single quote characters?

Now using the q function I don't need to do that:
<pre class="brush: sql;">DECLARE
  v_sql VARCHAR2 (255);
  v_result varchar2(255);
BEGIN
  v_sql := q'!select 'hello' into :a from dual !';

  EXECUTE IMMEDIATE v_sql
               INTO v_result;

  DBMS_OUTPUT.put_line (v_result);
END;</pre>
Notice now how "Hello" is wrapped as it would appear if it were not in variable definition function?

This can save you a lot of time by avoiding having to escape single quotes in strings!

_Update:&nbsp; [q Function Inside a q Function](http://www.talkapex.com/2012/03/q-function-inside-q-function.html)_