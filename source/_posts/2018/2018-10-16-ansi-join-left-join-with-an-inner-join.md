---
title: 'ANSI Join: Left Join with an Inner Join'
tags:
  - sql
date: 2018-10-16 19:54:21
alias:
---


I finally caved in and started to write all my queries using ANSI format (_thanks [Vincent Morneau](https://twitter.com/vincentmorneau) for encouraging me to change to ANSI_). I recently came across a situation where I needed to join two tables then outer join the result with another table. I could easily do this using Oracle joins (with an inner `select` statement) but was never a fan of it and wanted to see if there was a "cleaner" way to do this using ANSI joins.

Before continuing it's important to review some tables that are required for this demo:

- `dept`: 4 departments
- `emp`: 14 rows that contain 3 of the departments (i.e. one department has no employees)

When you do an outer join (as shown below), 15 rows are returned as one department isn't referenced in the `emp` table.

```sql
select count(1)
from dept d, emp e
where d.deptno = e.deptno(+)
;

COUNT(1)
      15
```

For this demo, a new table is required called `dept_country`. This will be used to join with the `emp` table to show the country each employee lives in:

```sql
create table dept_country as
select 
  to_number(regexp_replace(column_value,'(.*)-(.*)', '\1')) deptno,
  regexp_replace(column_value,'(.*)-(.*)', '\2') country
from 
  apex_string.split('10-Can,20-USA,30-Ger,40-Esp',',') x
;

select *
from dept_country
;

DEPTNO COUNTRY
    10 Can
    20 USA
    30 Ger
    40 Esp
```

If I try to "`join`" `emp` and `dept_country` without any special handling it will give me invalid data. The following query returns `14` rows when it should return `15`:

_Note for all the remaining examples the goal is to get `15` rows_

```sql
select d.deptno, d.dname, e.ename, dc.country
from 
  dept d,
  emp e,
  dept_country dc
where 1=1
  and d.deptno = e.deptno(+)
  and e.deptno = dc.deptno
;

DEPTNO DNAME        ENAME    COUNTRY
    10 ACCOUNTING   KING     Can
    10 ACCOUNTING   CLARK    Can
    10 ACCOUNTING   MILLER   Can
    20 RESEARCH     JONES    USA
    20 RESEARCH     SCOTT    USA
    20 RESEARCH     FORD     USA
    20 RESEARCH     SMITH    USA
    20 RESEARCH     ADAMS    USA
    30 SALES        BLAKE    Ger
    30 SALES        ALLEN    Ger
    30 SALES        WARD     Ger
    30 SALES        MARTIN   Ger
    30 SALES        TURNER   Ger
    30 SALES        JAMES    Ger

-- 14 rows (invalid)
```

The correct way to write the query using Oracle join syntax is shown below. I'm not a fan of the "inner `select`" technique as it takes more time to write and you need to list out all the columns that are required.

```sql
select d.deptno, d.dname, e.ename, e.country
from dept d,
  (
    select e.deptno, e.ename, dc.country
    from dept_country dc, emp e
    where 1=1
      and dc.deptno = e.deptno(+)
  ) e
where 1=1
  and d.deptno = e.deptno(+)
;

DEPTNO DNAME        ENAME    COUNTRY
    10 ACCOUNTING   KING     Can
    30 SALES        BLAKE    Ger
    10 ACCOUNTING   CLARK    Can
    20 RESEARCH     JONES    USA
    20 RESEARCH     SCOTT    USA
    20 RESEARCH     FORD     USA
    20 RESEARCH     SMITH    USA
    30 SALES        ALLEN    Ger
    30 SALES        WARD     Ger
    30 SALES        MARTIN   Ger
    30 SALES        TURNER   Ger
    20 RESEARCH     ADAMS    USA
    30 SALES        JAMES    Ger
    10 ACCOUNTING   MILLER   Can
    40 OPERATIONS

-- 15 rows (correct)
-- Note the extra row for OPERATIONS that has no employees
```

_For the rest of this article I won't be showing the results of each query as they are the same for the 14 vs 15 rows as they are the results are the same as the two previous examples._

Replicating the same situation using ANSI joins the following query is incorrect since the `emp` is outer joined but `dept_country` is `inner join`ed, thus negating the `outer` join.

```sql
select d.deptno, d.dname, e.ename, dc.country
from dual
  join dept d on 1=1
  left join emp e on 1=1
    and e.deptno = d.deptno
  join dept_country dc on 1=1
    and e.deptno = dc.deptno
;
-- 14 rows (invalid)
```

ANSI allows for brackets around joins to explicitly specify `inner` and then `outer` joins as shown below. I like this syntax as it's very clear to write, read, and for others to understand. It's also better than the Oracle inner `select` format as we don't need to list out all the columns in a separate `select` statement.

```sql
select d.deptno, d.dname, e.ename, dc.country
from dual
  join dept d on 1=1
  left join (
    dual
    join emp e on 1=1
    join dept_country dc on 1=1
      and e.deptno = dc.deptno
  ) on 1=1
    and e.deptno = d.deptno
;
-- 15 rows (correct)
```
