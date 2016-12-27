---
title: Setting SYSDATE in Oracle for Testing
tags:
  - PL/SQL
date: 2012-08-28 07:00:00
alias:
---

A while ago two very smart guys ([Cristian Ruepprich](http://ruepprich.wordpress.com/) and [Carsten Czarski](http://sql-plsql-de.blogspot.ca/)) had a conversation on Twitter about how to modify the value of <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SYSDATE</span> in Oracle for testing purposes. The ability to modify the value of <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SYSDATE</span> can be very valuable if you have to do time-sensitive testing.

Thankfully they did have a solution, by setting the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">FIXED_DATE</span> system parameter. Here's an example on how to use it:
<pre class="brush: sql; highlight: [10]">DECLARE
SQL> SELECT SYSDATE FROM dual;

SYSDATE
--------------------
22-AUG-2012 10:20:19

SQL> -- Do this now as priviledged user (ex: SYSTEM)
SQL> -- Note: this affects the entire database (not just your session)
SQL> ALTER SYSTEM SET fixed_date='2010-01-01-14:10:00';

System altered.

SQL> SELECT SYSDATE FROM dual;

SYSDATE
--------------------
01-JAN-2010 14:10:00

SQL> -- Reset SYSDATE back to "Current Time"
SQL> ALTER SYSTEM SET fixed_date=NONE;

System altered.

SQL> SELECT SYSDATE FROM dual;

SYSDATE
--------------------
22-AUG-2012 10:21:29
</pre>
They're a few things to know about <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">FIXED_DATE</span> before using it:

- Setting it requires access to <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">ALTER SYSTEM</span>. This can be mitigated by creating a procedure to handle this as a privledged user (see Tom Kyte's suggestion [here](http://asktom.oracle.com/pls/asktom/f?p=100:11:0::::P11_QUESTION_ID:26070651474562)).

- <span style="color:red;">It affects the entire system, not just your session</span>. If you have multiple users testing on a system then you may not be able to use it. Hopefully in the future we'll be able to modify it at a session level.

- It <span style="color:red;">only</span> affects SYSDATE and not date/time functions such as systimestamp (see Sean's comment below

The documentation for <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">FIXED_DATE</span> can be found [here](http://docs.oracle.com/cd/E11882_01/server.112/e25513/initparams093.htm#REFRN10062).