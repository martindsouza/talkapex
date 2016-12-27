---
title: How to Quickly Append VARCHAR2 to CLOB
tags:
  - plsql
date: 2009-06-15 09:00:00
alias:
---

<span style="font-style:italic">This is not an APEX specific issue, however it could be useful for some of your PL/SQL code</span>

I ran into an issue today where I had to append VARCHAR2s to a CLOB many times in a loop. I first tried appending a VARCHAR2 to a CLOB: CLOB := CLOB || VARCHAR2\. I noticed that this was taking a long time to run. In order to speed up the process I tried the following techniques:
- Create a "temp" CLOB (TMP_CLOB := VARCHAR2) and then appended it the clob CLOB := CLOB || CLOB
- Use the CLOB := CLOB || TO_CLOB(VARCHAR2)
- Use DBMS_LOB.append (CLOB, VARCHAR2)

All three options resulted in significant speed increases, however using the "temp" CLOB method resulted in the quickest code. Here is the test that I ran along with the results:

<pre class="brush: sql">
DECLARE
  v_start TIMESTAMP;
  v_end TIMESTAMP;
  v_clob CLOB;
  v_tmp_clob CLOB;
  v_iterations PLS_INTEGER := 100000; -- Used 1,000, 10,000, and 100,000 for testing
BEGIN
  v_start := SYSTIMESTAMP;
  v_clob := NULL;

  FOR i IN 1 .. v_iterations LOOP
    v_clob := v_clob || TO_CHAR (SYSTIMESTAMP) || ', ';
  END LOOP;

  v_end := SYSTIMESTAMP;
  DBMS_OUTPUT.put_line ('CLOB := CLOB || VARCHAR2 method: ' || TO_CHAR (v_end - v_start));
  v_start := SYSTIMESTAMP;
  v_clob := NULL;

  FOR i IN 1 .. v_iterations LOOP
    v_clob := v_clob || TO_CLOB (TO_CHAR (SYSTIMESTAMP) || ', ');
  END LOOP;

  v_end := SYSTIMESTAMP;
  DBMS_OUTPUT.put_line ('CLOB := CLOB || TO_CLOB(VARCHAR2) method: ' || TO_CHAR (v_end - v_start));
  v_start := SYSTIMESTAMP;
  v_clob := NULL;

  FOR i IN 1 .. v_iterations LOOP
    v_tmp_clob := TO_CHAR (SYSTIMESTAMP) || ', ';
    v_clob := v_clob || v_tmp_clob;
  END LOOP;

  v_end := SYSTIMESTAMP;
  DBMS_OUTPUT.put_line ('CLOB := CLOB || TMP_CLOB method: ' || TO_CHAR (v_end - v_start));
  v_start := SYSTIMESTAMP;
  v_clob := NULL;
  v_clob := 'h'; -- need to initialize it;

  FOR i IN 1 .. v_iterations LOOP
    DBMS_LOB.append (v_clob, TO_CHAR (SYSTIMESTAMP) || ', ');
  END LOOP;

  v_end := SYSTIMESTAMP;
  DBMS_OUTPUT.put_line ('DBMS_LOB.append method: ' || TO_CHAR (v_end - v_start));
END;
</pre>

The results were as follows:

<span style="font-weight:bold">1,000 Iterations</span>
CLOB := CLOB || VARCHAR2 method: +000000000 00:00:00.578000000
CLOB := CLOB || TO_CLOB(VARCHAR2) method: +000000000 00:00:00.063000000
CLOB := CLOB || TMP_CLOB method: +000000000 00:00:00.047000000
DBMS_LOB.append method: +000000000 00:00:00.172000000

<span style="font-weight:bold">10,000 Iterations</span>
CLOB := CLOB || VARCHAR2 method: +000000000 00:00:10.656000000
CLOB := CLOB || TO_CLOB(VARCHAR2) method: +000000000 00:00:00.688000000
CLOB := CLOB || TMP_CLOB method: +000000000 00:00:00.672000000
DBMS_LOB.append method: +000000000 00:00:00.687000000

<span style="font-weight:bold">100,000 Iterations</span>
CLOB := CLOB || VARCHAR2 method: +000000000 00:42:17.453000000
CLOB := CLOB || TO_CLOB(VARCHAR2) method: +000000000 00:00:17.953000000
CLOB := CLOB || TMP_CLOB method: +000000000 00:00:08.140000000
DBMS_LOB.append method: +000000000 00:00:11.110000000
