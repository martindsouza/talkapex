---
title: 'Oracle: Advanced Error Messages'
tags:
  - ORACLE
  - PL/SQL
date: 2009-07-22 09:00:00
alias:
---

<span style="font-style:italic;">This is not an APEX specific post, however it can be useful for error handling</span>.

A colleague showed me a great way to get more useful debug information. Normally I used <span style="font-style:italic;">SQLERRM</span> and <span style="font-style:italic;">SQLCODE</span> in an exception to display or store error messages. Using [DBMS_UTILITY](http://download.oracle.com/docs/cd/B19306_01/appdev.102/b14258/d_util.htm) you can get more detailed Oracle error messages. Here's an example:

<pre class="brush: sql">
-- I put this in a package for demo purposes
CREATE OR REPLACE PACKAGE pkg_err_test
AS
  PROCEDURE sp_err_test (
    p_empno IN emp.empno%TYPE
  );
END pkg_err_test;

CREATE OR REPLACE PACKAGE BODY pkg_err_test
AS
  PROCEDURE sp_err_test (
    p_empno IN emp.empno%TYPE
  )
  AS
    v_ename emp.ename%TYPE;
  BEGIN
    SELECT ename
      INTO v_ename
      FROM emp
     WHERE empno = p_empno;

    DBMS_OUTPUT.put_line ('Employee name is: ' || v_ename);
  EXCEPTION
    WHEN OTHERS THEN
      -- Basic Error Message
      DBMS_OUTPUT.put_line ('Old Error Message: ' || SUBSTR (SQLERRM, 1, 255));
      DBMS_OUTPUT.put_line ('Old Err Code: ' || SQLCODE);
      -- Advanced Error Messages
      DBMS_OUTPUT.put_line ('-- New Error Messages --');
      DBMS_OUTPUT.put_line (DBMS_UTILITY.format_error_stack);   -- Error Message
      DBMS_OUTPUT.put_line (DBMS_UTILITY.format_error_backtrace);   -- Where it occurred
      DBMS_OUTPUT.put_line (DBMS_UTILITY.format_call_stack);   -- Call Stack
  END sp_err_test;
END pkg_err_test;

-- Run the error test with an invalid employee number so an exception will be raised
EXEC pkg_err_test.sp_err_test(p_empno => 123);
</pre>

DBMS Output:

> Old Error Message: ORA-01403: no data found
> Old Err Code: 100
> -- New Error Messages --
> ORA-01403: no data found
> 
> ORA-06512: at "GIFFY.PKG_ERR_TEST", line 9
> 
> ----- PL/SQL Call Stack -----
>   object      line  object
>   handle    number  name
> 362D7814        24  package body GIFFY.PKG_ERR_TEST
> 362D70E0         1  anonymous block

The error message is displayed as well as where the error occurred and the call stack. In large systems this can be very helpful. You should be aware that when called from a package, it does not list the procedure or function (as seen in this example) where the error occurred so you may need to hard code the function or procedure name in your error message.