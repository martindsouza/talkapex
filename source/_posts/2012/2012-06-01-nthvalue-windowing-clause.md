---
title: NTH_VALUE Windowing Clause
tags:
  - sql
date: 2012-06-01 07:55:00
alias:
---

In my [previous post](http://www.talkapex.com/2012/05/some-interesting-oracle-analytic.html), which highlighted some analytic functions, I mentioned that the windowing clause must be explicitly defined when using the [NTH_VALUE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions114.htm#CJAFEJBE) function.

To recap, here's the example I used for NTH_VALUE which lists the 2nd highest salary for each department:
<pre class="brush: sql; highlight: 5">SELECT d.dname, e.ename, e.sal,
   nth_value(e.sal, 2) OVER (
    PARTITION BY e.deptno ORDER BY e.sal DESC
    -- windowing_clause
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) sec_high_sal_dept
FROM emp e, dept d
WHERE e.deptno = d.deptno;

-- Result

DNAME          ENAME             SAL SEC_HIGH_SAL_DEPT
-------------- ---------- ---------- -----------------
ACCOUNTING     KING             5000              2450
ACCOUNTING     CLARK            2450              2450
ACCOUNTING     MILLER           1300              2450
RESEARCH       FORD             3000              3000
RESEARCH       SCOTT            3000              3000
RESEARCH       JONES            2975              3000
RESEARCH       ADAMS            1100              3000
RESEARCH       SMITH             800              3000
SALES          BLAKE            2850              1600
SALES          ALLEN            1600              1600
SALES          TURNER           1500              1600
SALES          WARD             1250              1600
SALES          MARTIN           1250              1600
SALES          JAMES             950              1600
</pre>What happens if we don't include the windowing clause? Here's the same query, but just focusing on the Accounting department, without the windowing clause:
<pre class="brush: sql; highlight: 12">SELECT d.dname, e.ename, e.sal,
   nth_value(e.sal, 2) OVER (
    PARTITION BY e.deptno ORDER BY e.sal DESC) sec_high_sal_dept
FROM emp e, dept d
WHERE e.deptno = d.deptno
  AND d.dname = 'ACCOUNTING';

-- Result

DNAME          ENAME             SAL SEC_HIGH_SAL_DEPT
-------------- ---------- ---------- -----------------
ACCOUNTING     KING             5000
ACCOUNTING     CLARK            2450              2450
ACCOUNTING     MILLER           1300              2450
</pre>You'll notice that the first row (KING) has a NULL returned for the SEC_HIGH_SAL_DEPT column. That's because when it looks at the first row (KING) it still hasn't had a chance to evaluate at least 2 values.   Obviously writing some test queries will identify this "issue" which may or may not be what you're looking for. If it isn't then just add the windowing clause (above).
