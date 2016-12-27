---
title: NTILE vs WIDTH_BUCKET
tags:
  - sql
date: 2014-04-12 21:40:00
alias:
---

Rick Cale recently asked a question on an article I wrote two years ago about [Some Interesting Oracle Analytic Functions](http://www.talkapex.com/2012/05/some-interesting-oracle-analytic.html). His question (see comments on Apr 11, 2014) were about the results from the [NTILE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions115.htm#SQLRF00680) function and how the same value could be in two different buckets. It was an excellent question and one that got me digging a bit more into the functionality of NTILE.

They're two ways to handle "bucketing data" in Oracle. In the documentation Oracle describes the two types as either having equiwidth or or equiheight histograms.
**
****Equiwidth** ([NTILE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions115.htm#SQLRF00680)): Each bucket will have the same number of items in it with some buckets having at most 1 more item than other buckets. An easy way to think of this concept is to order all the items, then divide the data evenly into groups based on the number of buckets. For example, supposed you have 9 values (AAAABCDEF) and wanted to put them into 3 buckets. the buckets would be B1 = AAA, B2 = ABC, B3 = DEF. You'll notice that the value "A" is in both buckets B1 <u>and</u>&nbsp;B2.

When an uneven amount of objects need to go into the buckets, NTILE will fill the first bucket first, second bucket second, etc. For example, suppose you had 10 values (AAAABCDEFG) and wanted to fill them into 3 buckets. (_Note this is similar to the previous example with an additional "G")_. The buckets would be B1 = AAAA, B2 = BCD, B3 = EFG.

<div>**Equiheight** ([WIDTH_BUCKET](http://docs.oracle.com/cd/E11882_01/server.112/e41084/functions234.htm#SQLRF06163)): This will take the min and max range, divided by the number of buckets and place each value in it. For example, the salaries in the EMP table range from 801 ~ 5000\. If you set the min/max range from 0~5000 3 buckets will be created. All values from 0~1,666 will go into B1, values from 1,6667~3333 into B2, and values 3334 to 5000 into B3\. _(Note: for simplicity I took out decimals in this split).&nbsp;_</div><div>
</div><div>
</div><div>An easy way to think of these two bucketing methods is that NTILE divides values based on the number of items. WIDTH_BUCKET divides values based on their values.</div><div><div>
</div><div>Here's an example that highlights the difference between the two functions.&nbsp;</div><pre class="brush: sql;">with data as (
  -- using this as data input
  select 3 as num_buckets
  from dual
)
select
  d.dname,
  e.ename,
  e.sal,
  ntile (data.num_buckets) over (order by sal asc) ntile,
  width_bucket(sal, 0, max(sal+1) over (), data.num_buckets) width_bucket
from emp e, dept d, data
where e.deptno = d.deptno
order by sal;

DNAME          ENAME      SAL        NTILE      WIDTH_BUCKET
-------------- ---------- ---------- ---------- ------------
RESEARCH       SMITH         801          1        1
SALES          JAMES         950          1        1
RESEARCH       ADAMS        1100          1        1
SALES          WARD         1250          1        1
SALES          MARTIN       1250          1        1
ACCOUNTING     MILLER       1300          2        1
SALES          TURNER       1500          2        1
SALES          ALLEN        1600          2        1
ACCOUNTING     CLARK        2450          2        2
RESEARCH       JONES        2975          2        2
RESEARCH       SCOTT        3000          3        2
RESEARCH       FORD         3000          3        2
SALES          BLAKE        3850          3        3
ACCOUNTING     KING         5000          3        3
</pre><div>It's important to note that WIDTH_BUCKET is not an analytic function but NTILE is. For more information read the documentation for each function. For WIDTH_BUCKET, the documentation covers what happens with values outside the min/max range (they go into bucket 0 and num_buckets+1).</div></div>
