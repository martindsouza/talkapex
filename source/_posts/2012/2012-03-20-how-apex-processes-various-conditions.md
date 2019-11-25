---
title: How APEX Processes Various Conditions and Validations
tags:
  - apex
date: 2012-03-20 11:05:00
alias:
---

I was recently teaching an Intro to APEX course and the students had some questions about what to put in the _Expression 1_ text area for different conditions and validations based on the _Type_ selected. If you're new to APEX the various options can be confusing.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-bkOzklcOyIc/T2i16zitiyI/AAAAAAAAEG0/10kpVAfRl5g/s640/condition_types.png)](http://3.bp.blogspot.com/-bkOzklcOyIc/T2i16zitiyI/AAAAAAAAEG0/10kpVAfRl5g/s1600/condition_types.png)</div>
To help clear things up I've included a list of the various APEX conditions and validations along with code that demonstrates how APEX processes the value in _Expression 1_ based on the given _Type_. I didn't include the definition for each type as they are already well documented in the APEX documentation and popup help.

_Note: the calls to DBMS_OUTPUT are there to show you if the validation/condition is true or false._

**Exists**
<pre class="brush: sql; highlight: [8,9,10]">DECLARE
  l_rows pls_integer;
BEGIN
  SELECT count(1)
  INTO l_rows
  FROM (
    -- Start Expression 1
    SELECT 1
    FROM emp
    WHERE sal &gt; :p1_sal
    -- End Expression 1
  );

  IF l_rows &gt; 0 THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE');
  END IF;
END;
/
</pre>**Not Exists**
<pre class="brush: sql; highlight: [8, 9, 10]">DECLARE
  l_rows pls_integer;
BEGIN
  SELECT count(1)
  INTO l_rows
  FROM (
    -- Start Expression 1
    SELECT 1
    FROM emp
    WHERE sal &gt; :p1_sal
    -- End Expression 1
  );

  IF l_rows = 0 THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE');
  END IF;
END;
/
</pre>**SQL Expression**
_Note: SQL Expression and PL/SQL Expression are very similar but some SQL expressions can't be used in PL/SQL. Example: Decode_
<pre class="brush: sql; highlight: [9]">DECLARE
  l_rows pls_integer;
BEGIN
  SELECT count(1)
  INTO l_rows
  FROM dual
  WHERE
    -- Start Expression 1
    :p1_sal &gt; 500
    -- End Expression 1
  ;

  IF l_rows = 1 THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE');
  END IF;
END;
/
</pre>**PL/SQL Expression**
<pre class="brush: sql; highlight: [5]">DECLARE
BEGIN
  IF (
    -- Start Expression 1
    :p1_sal &gt; 500
    -- End Expression 1
  ) THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE');
  END IF;

END;
/
</pre>**PL/SQL Function Returning Boolean**
<pre class="brush: sql; highlight: [6,7,8,9,10,11,12,13,14,15,16,17,18,19]">DECLARE
  FUNCTION f_apex_condtion RETURN boolean
  AS
  BEGIN
    -- Start Expression 1
    DECLARE
      l_rows pls_integer;
    BEGIN
      SELECT count(*)
      INTO l_rows
      FROM emp
      WHERE JOB = 'PRESIDENT';

      IF l_rows &gt; 1 THEN -- only want at most 1 president
        RETURN FALSE;
      ELSE
        RETURN TRUE;
      END IF;
    END;
    -- End Expression 1
  END;

BEGIN

  IF f_apex_condtion THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE');
  END IF;

END;
/
</pre>**_Conditions Only_**

**Function Returning Error Text**
<pre class="brush: sql; highlight: [8,9,10,11,12,13,14,15,16,17,18,19,20,21]">DECLARE
  l_err_msg varchar2(4000);

  FUNCTION f_apex_condtion RETURN varchar2
  AS
  BEGIN
    -- Start Expression 1
    DECLARE
      l_rows pls_integer;
    BEGIN
      SELECT count(*)
      INTO l_rows
      FROM emp
      WHERE JOB = 'PRESIDENT';

      IF l_rows &gt; 1 THEN
        RETURN 'Only 1 president can exist for the company';
      ELSE
        RETURN NULL; -- no error
      END IF;
    END;
    -- End Expression 1
  END;

BEGIN
  l_err_msg := f_apex_condtion;

  IF l_err_msg IS NULL THEN
    dbms_output.put_line('TRUE');
  ELSE
    dbms_output.put_line('FALSE - Error message: ' || l_err_msg);
  END IF;

END;
/
</pre>**PL/SQL Error**
<pre class="brush: sql; highlight: [6,7,8,9,10,11,12,13,14,15,16,17]">DECLARE

  PROCEDURE sp_apex_condtion AS
  BEGIN
    -- Start Expression 1
    DECLARE
      l_rows pls_integer;
    BEGIN
      SELECT count(*)
      INTO l_rows
      FROM emp
      WHERE JOB = 'PRESIDENT';

      IF l_rows &gt; 1 THEN
        raise_application_error(-20001, 'Only 1 president can exist for the company');
      END IF;
    END;
    -- End Expression 1
  END;

BEGIN
  sp_apex_condtion;

  dbms_output.put_line('TRUE');

exception WHEN others THEN
  dbms_output.put_line('FALSE'); -- Error message is defined in the validation's error message
END;
/
</pre>
