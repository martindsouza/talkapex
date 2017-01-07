---
title: SELECT INTO Techinques
tags:
  - plsql
date: 2012-06-07 11:01:00
alias:
---

One thing that bothers a lot of PL/SQL developers is when they have to select some data from a table into local variables. The problem is that the data may or may not exist (i.e. return 0..1 rows). They're several ways to write this code, some taking longer than others which is the pain point for developers.  

I've listed out various ways to do this below and hopefully it'll help you find some easier ways to solve this issue.

 The first example is the classic case of handling the 0..1 rows issue in PL/SQL. They're a few issues (not problems) that I have with this. The first is that it adds some additional lines of code (highlighted) which can make the code harder to follow. The other issue is that instead of using "_WHEN no_data_found_" some developers tend to use "_WHEN OTHERS_" which is guaranteed to give you some false positives in the long run.  <pre class="brush: sql; highlight: [6,11,12,13,14]">
DECLARE
  l_sal emp.sal%TYPE;
  p_empno emp.empno%TYPE := 123; -- Invalid empno number
BEGIN

  BEGIN
    SELECT sal
    INTO l_sal
    FROM emp
    WHERE empno = p_empno;
  EXCEPTION
    WHEN no_data_found THEN
      l_sal := 0;
  END;

END;
/
</pre> Another way to get the value from the table is to use the aggregate function _MAX_. Since you're expecting 0..1 rows this will always return 1 row and eliminates the need to have the "_BEGIN ..._" block of code which saves 5 lines of code. You can handle what to do when no value is found by using the _NVL_ function.  <pre class="brush: sql; highlight: [6]">
DECLARE
  l_sal emp.sal%TYPE;
  p_empno emp.empno%TYPE := 123; -- Invalid empno number
BEGIN

  SELECT NVL(MAX(sal), 0)
  INTO l_sal
  FROM emp
  WHERE empno = p_empno;

END;
/
</pre> The only issue with the above example is when you can have 0..n rows but should only get 0..1\. Ideally you should have table constraints in order to guarantee 0..1 rows but it's not always the case in some system. The following example shows how you can use the same technique as above but it will trigger an exception if n rows are found (which is what you want to do):  <pre class="brush: sql; highlight: [10]">
DECLARE
  l_sal emp.sal%TYPE;
  p_empno emp.empno%TYPE := 123; -- Invalid empno number
BEGIN

  SELECT NVL(MAX(sal), 0)
  INTO l_sal
  FROM emp
  WHERE empno = p_empno
  HAVING count(1) <= 1;

END;
/
</pre>
