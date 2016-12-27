---
title: How To Reference Package Variables Outside of PL/SQL
tags:
  - ORACLE
  - PL/SQL
date: 2010-03-24 09:00:00
alias:
---

When developing with PL/SQL you may store public variables in the package specification. This has many uses, none of which I will get into for this post. The only catch in Oracle is that you can not easily reference these values in SQL statements outside of PL/SQL. The following example demonstrates this:

- Create Package Spec with Variable
<pre class="brush: sql">
CREATE OR REPLACE PACKAGE pkg_var
AS
  c_my_var   CONSTANT VARCHAR2 (5) := 'hello';
END pkg_var;
</pre>

- Reference the variable in a SQL statement in SQL*Plus
<pre class="brush: sql">
SQL> SELECT pkg_var.c_my_var x
  2    FROM DUAL;
SELECT pkg_var.c_my_var x
       *
ERROR at line 1:
ORA-06553: PLS-221: 'C_MY_VAR' is not a procedure or is undefined
</pre>
<span style="font-style:italic;">This results in an Oracle error.</span>

- Try the same code, but in an block of PL/SQL
<pre class="brush: sql">
SQL> DECLARE
  2    v_x   VARCHAR2 (5);
  3  BEGIN
  4    SELECT pkg_var.c_my_var x
  5      INTO v_x
  6      FROM DUAL;
  7
  8    DBMS_OUTPUT.put_line (v_x);
  9  END;
 10  /
hello

PL/SQL procedure successfully completed.
</pre>
<span style="font-style:italic;">As you can see this worked.</span>

So how can we refernce package variables in a non-PL/SQL setting? I created the following function to do so. It will handle values that are of type VARCHAR2\. I've also removed any spaces from the parameter (pkg_name.var_name) to ensure that no SQL injection will occur.
<pre class="brush: sql">
-- **
-- * Returns Package Variable value
-- * Note: for demo purposes I broke this function into various steps
-- * 
-- * @param p_pkg_var_name fully qualified variable reference. Ex: pkg_x.var_y
-- * @return Varchar2 value
-- * @author Martin Giffy D'Souza: http://apex-smb.blogspot.com
-- **
CREATE OR REPLACE FUNCTION f_get_pkg_val_vc2 (p_pkg_var_name in varchar2)
  RETURN VARCHAR2
AS
  v_string          VARCHAR2 (4000);
  -- Full Variable Name (i.e. pkg.var)
  v_var_full_name   VARCHAR2 (61);                             -- Max of 61 chars since 30 + . + 30
BEGIN
  v_var_full_name := p_pkg_var_name;
  -- Remove any spaces to avoid SQL injections
  v_var_full_name := REGEXP_REPLACE (v_var_full_name, '[[:space:]]', '');

  EXECUTE IMMEDIATE 'begin :v_string := ' || v_var_full_name || '; end;'
              USING OUT v_string;

  RETURN v_string;
END f_get_pkg_val_vc2;
</pre>

Now when you run in SQL*Plus you get the following:
<pre class="brush: sql">
SQL> SELECT f_get_pkg_val_vc2 ('pkg_var.c_my_var') x
  2    FROM DUAL;

X
-----------------------------------------------------

hello
</pre>