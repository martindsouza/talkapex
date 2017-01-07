---
title: PL/SQL Exceptions Propagation during Variable Declaration
tags:
  - plsql
date: 2012-09-04 07:00:00
alias:
---

It's always good to know how any language handles and propagates exceptions, Oracle PL/SQL being no different. They're plenty of examples online about raising and handling exceptions on the web, but one thing you may not have realized is how PL/SQL propagates exceptions that occur in the variable declaration section of a procedure.

In the first example I created a procedure that has a variable, <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">l_var</span>, which can handle one character. As expected, when I assign more then one character an exception is raised and is propagated to the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">EXCEPTION</span> block of the procedure.
<pre class="brush: sql; highlight: [6,16,17,18]">
SQL> CREATE OR REPLACE PROCEDURE sp_test(p_var in varchar2)
  2  AS
  3    l_var VARCHAR2(1);
  4  BEGIN
  5    dbms_output.put_line('***START***');
  6    l_var := 'abc';
  7  exception
  8    WHEN others THEN
  9      dbms_output.put_line('***Exception***');
 10      raise;
 11  END sp_test;
 12  /

Procedure created.

SQL> exec sp_test(p_var => 'abc');
***START***
***Exception***
BEGIN sp_test(p_var => 'abc'); END;

*
ERROR at line 1:
ORA-06502: PL/SQL: numeric or value error: character string buffer too small
ORA-06512: at "ODTUG.SP_TEST", line 10
ORA-06512: at line 1
</pre>In the next example, instead of assigning the value in the main block of code I assigned the value in the declaration section. You'll notice that the procedure doesn't even get to the "START" line nor is the exception handled in the procedure's exception block. Instead the exception is propagated to the calling process right away.
<pre class="brush: sql; highlight: [3,15]">
SQL> CREATE OR REPLACE PROCEDURE sp_test(p_var in varchar2)
  2  AS
  3    l_var VARCHAR2(1) := p_var;
  4  BEGIN
  5    dbms_output.put_line('***START***');
  6  exception
  7    WHEN others THEN
  8      dbms_output.put_line('***Exception***');
  9      raise;
 10  END sp_test;
 11  /

Procedure created.

SQL> exec sp_test(p_var => 'abc');
BEGIN sp_test(p_var => 'abc'); END;

*
ERROR at line 1:
ORA-06502: PL/SQL: numeric or value error: character string buffer too small
ORA-06512: at "ODTUG.SP_TEST", line 3
ORA-06512: at line 1
</pre>Before you go and change any of your existing code based on this article, I'm not saying that you should avoid defining variables in the declaration section of a procedure. Instead, just be aware of how the exception is propagated. This can be useful to know if your local variable is assigned to an input parameter. In that case you may want to assign the local variable in the main block of code rather then in the variable declaration section.

For documentation of how PL/SQL propagates exceptions raised in declarations go [here](http://docs.oracle.com/cd/E11882_01/appdev.112/e25519/errors.htm#LNPLS00701). If you haven't already done so, I'd recommend reading the entire [PL/SQL Error Handling](http://docs.oracle.com/cd/E11882_01/appdev.112/e25519/errors.htm) documentation.
