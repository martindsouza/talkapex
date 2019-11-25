---
title: Some Interesting Oracle Analytic Functions
tags:
  - sql
date: 2012-05-22 07:00:00
alias:
---

I had to write some reports for a personal application of mine last night and needed to expand beyond my usual set of analytic functions that I normally use. I thought it would be a good idea to do a quick blog post on these analytic functions.

_If you've never heard of or used Oracle analytic functions please read [this article](http://orafaq.com/node/55) first. It's by far the best article I've read at explaining what analytic functions are and how to use them. The Oracle analytic function documentation can be found [here](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions004.htm#SQLRF06174)._

**RATIO_TO_REPORT**

[RATIO_TO_REPORT](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions142.htm#i85800) compares the current value against the sum of the other set of values.   The following example shows the percentage of each employees salary when compared to the sum of their department's salary. You'll notice that you can calculate the same value using a different method (_sal_dept_ratio2_) which may be why I haven't seen RATIO_TO_REPORT used that often.  
<pre class="brush: sql; highlight: 2">SELECT d.dname, e.ename, e.sal,
  round(ratio_to_report(sal) OVER (PARTITION BY e.deptno),2) sal_dept_ratio,
  round(sal / sum(e.sal) OVER (PARTITION BY e.deptno),2) sal_dept_ratio2
FROM emp e, dept d
WHERE e.deptno = d.deptno;

-- Result

DNAME          ENAME             SAL SAL_DEPT_RATIO SAL_DEPT_RATIO2
-------------- ---------- ---------- -------------- ---------------
ACCOUNTING     CLARK            2450            .28             .28
ACCOUNTING     MILLER           1300            .15             .15
ACCOUNTING     KING             5000            .57             .57
RESEARCH       FORD             3000            .28             .28
RESEARCH       SCOTT            3000            .28             .28
RESEARCH       JONES            2975            .27             .27
RESEARCH       SMITH             800            .07             .07
RESEARCH       ADAMS            1100             .1              .1
SALES          WARD             1250            .13             .13
SALES          MARTIN           1250            .13             .13
SALES          TURNER           1500            .16             .16
SALES          JAMES             950             .1              .1
SALES          ALLEN            1600            .17             .17
SALES          BLAKE            2850             .3              .3
</pre>
**NTH_VALUE**

[NTH_VALUE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions114.htm#CJAFEJBE) returns the nth row in the window clause. It's extremely important that you <u>explicitly define the window</u> or your values may not make sense. I'll write another post explaining this later. _Update: [NTH_VALUE Windowing Clause](http://www.talkapex.com/2012/06/nthvalue-windowing-clause.html)_

The following example shows the 2nd highest salary in each department.  
<pre class="brush: sql; highlight: [2,3,4,5]">SELECT d.dname, e.ename, e.sal,
   nth_value(e.sal, 2) OVER (
    PARTITION BY e.deptno ORDER BY e.sal DESC
    -- Don't forget to define the window
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
</pre>
**LISTAGG**

[LISTAGG](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions089.htm#CJABDFBD) was a highly requested feature that was implemented in 11gR2\. Overall LISTAGG allows you to group values in multiple rows and put them in a comma delimited column on one row.

The following example (which is not an analytic function) shows all the employees for each department:  
<pre class="brush: sql; highlight: 2">SELECT d.dname,
  listagg(e.ename, ',') WITHIN GROUP (ORDER BY e.ename) dept_emp_list
FROM emp e, dept d
WHERE e.deptno = d.deptno
GROUP BY d.dname;

-- Result

DNAME          DEPT_EMP_LIST
-------------- ----------------------------------------

ACCOUNTING     CLARK,KING,MILLER
RESEARCH       ADAMS,FORD,JONES,SCOTT,SMITH
SALES          ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
</pre>Using the LISTAGG as an analytic function you can see each employee's colleagues in their department, including themselves.  
<pre class="brush: sql; highlight: [2,3]">SELECT d.dname, e.ename,
  listagg(e.ename, ',') WITHIN GROUP (ORDER BY e.ename)
    OVER (PARTITION BY e.deptno) dept_colleagues
FROM emp e, dept d
WHERE e.deptno = d.deptno;

-- Result

DNAME          ENAME      DEPT_COLLEAGUES
-------------- ---------- --------------------------------------

ACCOUNTING     CLARK      CLARK,KING,MILLER
ACCOUNTING     KING       CLARK,KING,MILLER
ACCOUNTING     MILLER     CLARK,KING,MILLER
RESEARCH       ADAMS      ADAMS,FORD,JONES,SCOTT,SMITH
RESEARCH       FORD       ADAMS,FORD,JONES,SCOTT,SMITH
RESEARCH       JONES      ADAMS,FORD,JONES,SCOTT,SMITH
RESEARCH       SCOTT      ADAMS,FORD,JONES,SCOTT,SMITH
RESEARCH       SMITH      ADAMS,FORD,JONES,SCOTT,SMITH
SALES          ALLEN      ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
SALES          BLAKE      ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
SALES          JAMES      ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
SALES          MARTIN     ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
SALES          TURNER     ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
SALES          WARD       ALLEN,BLAKE,JAMES,MARTIN,TURNER,WARD
</pre>
**NTILE**

[NTILE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions115.htm#i85619) allows you to divide your values into buckets and see which bucket the current row belongs to. For educational institutions this is a very good function to let students know that they're in the top _x%_ of the class.

_**Update (12-Apr-2014):** [NTILE vs WIDTH_BOX](http://www.talkapex.com/2014/04/ntile-vs-widthbucket.html) describes the different ways to bucket data in Oracle. It also explains why SAL 1250 exists in both buckets 2 and 3._

The following example shows the 3 tiers (or buckets) of salaries across the entire company. It allows you to easily see who's in the top 33% of salaries.   
<pre class="brush: sql; highlight: [2]">SELECT d.dname, e.ename, e.sal,
  ntile (3) over (order by sal desc) three_tier_sal
FROM emp e, dept d
WHERE e.deptno = d.deptno;

-- Result

DNAME          ENAME             SAL THREE_TIER_SAL
-------------- ---------- ---------- --------------
ACCOUNTING     KING             5000              1
RESEARCH       SCOTT            3000              1
RESEARCH       FORD             3000              1
RESEARCH       JONES            2975              1
SALES          BLAKE            2850              1
ACCOUNTING     CLARK            2450              2
SALES          ALLEN            1600              2
SALES          TURNER           1500              2
ACCOUNTING     MILLER           1300              2
SALES          WARD             1250              2
SALES          MARTIN           1250              3
RESEARCH       ADAMS            1100              3
SALES          JAMES             950              3
RESEARCH       SMITH             800              3
</pre>
