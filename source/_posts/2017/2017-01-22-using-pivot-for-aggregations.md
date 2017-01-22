---
title: Using Pivot for Aggregations
tags:
  - sql
date: 2017-01-22 15:45:28
alias:
---


Suppose you had a requirement in which you needed to return one row which had two columns. The two columns would contain the number of employees in the two different departments. This sounds like a trivial problem to solve but isn't as easy when you get to coding it.

One way to do this is to use `case` statements in the aggregation function such as :

```sql
select
  count(case when d.dname = 'ACCOUNTING' then 1 else null end) acct_dept_cnt,
  count(case when d.dname = 'RESEARCH' then 1 else null end) rsch_dept_cnt
from emp e, dept d
where 1=1
  and e.deptno = d.deptno
;

ACCT_DEPT_CNT  RSCH_DEPT_CNT
3              5
```

Another neat way to do this is to use the `pivot` function:
```sql
select
  acct_dept_cnt,
  rsch_dept_cnt
from (
  select e.deptno, d.dname
  from emp e, dept d
  where 1=1
    and e.deptno = d.deptno)
pivot (count(deptno) as dept_cnt for (dname) in ('ACCOUNTING' as  acct, 'RESEARCH' as rsch))
;
```

In this example they both have the same explain plan as shown below. If using on larger / more complex data sets it would be a good idea to compare the explain plans for both queries to see if there's performance gain between the two.

{% asset_img explain-plan.png "" %}
