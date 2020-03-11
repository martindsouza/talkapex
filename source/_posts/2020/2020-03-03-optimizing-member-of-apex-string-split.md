---
title: Optimizing MEMBER OF with APEX_STRING.SPLIT
tags:
  - apex
  - sql
date: 2020-03-03 07:00:00
alias:
---


[`apex_string.split`](https://docs.oracle.com/en/database/oracle/application-express/19.2/aeapi/SPLIT-Function-Signature-1.html#GUID-3BE7FF37-E54F-4503-91B8-94F374E243E6) is a great utility that allows you to take a delimited list and make it queryable. Here's a small example:

```sql
select column_value
from apex_string.split('John,Sally,Bob', ',')
;

   COLUMN_VALUE
_______________
John
Sally
Bob
```

You can read more about using `apex_string.split` and how to parse CSVs {% post_link query-csv-data-using-apex-5-1-apis here %}.

When I use the `split` function to filter a table I used to do an explicit `join` as shown below. There's nothing wrong with this concept but it can be a bit clunky to read and write.

```sql
select e.*
from dual
  join emp e on 1=1
  join (select column_value job from apex_string.split('CLERK,MANAGER', ',')) x on 1=1
    and x.job = e.job
;
```

Recently [Stefan Dobre](https://twitter.com/stefan__dobre) [tweeted](https://twitter.com/stefan__dobre/status/1230242479569559552?s=11) about how you can use `value member of apex_string.split(...)` to accomplish the same thing. Here's an example:

```sql
select *
from emp e
where 1=1
  and e.job member of apex_string.split('CLERK,MANAGER', ',')
;
```

Using `member of` is a nice clean solution however since `apex_string.split` is a PL/SQL function there's the potential of a lot of context switching (between SQL and PL/SQL) that can slow your code down. To verify this I made a simple wrapper function which logs all the calls to the function:

```sql
create or replace function wrapper_split(
  p_str in varchar2,
  p_sep in varchar2)
  return wwv_flow_t_varchar2
as
begin
  logger.log('START', 'wrapper_split');
  return apex_string.split(
    p_str => p_str,
    p_sep => p_sep
  );
end wrapper_split;
/
```

Using the previous example:

```sql
select *
from emp e
where 1=1
  and e.job member of wrapper_split('CLERK,MANAGER', ',')
;

-- ...

-- Check Logger:
select count(1) cnt
from logger_logs_5_min
where scope = 'wrapper_split'
;

   CNT
______
    14

```

They're 14 rows in the `emp` table and `apex_string.split` was called 14 times. When using this on larger tables it could have significant performance impacts. Thankfully there's a simple solution: [scalar subqueries](https://oracle-base.com/articles/misc/efficient-function-calls-from-sql#scalar-subquery-caching).

```sql
select *
from emp e
where 1=1
  and e.job member of (select wrapper_split('CLERK,MANAGER', ',') from dual)
;

-- ...

-- Check Logger:
select count(1) cnt
from logger_logs_5_min
where scope = 'wrapper_split'
-- Note extra predicate was added to only show logged records after the previous example
;

   CNT
______
     1
```

This subtle difference limits it to one PL/SQL call regardless of the size of the table it's checking. If using the `member of` syntax don't forget to also reference `apex_string.split` in a scalar subquery.