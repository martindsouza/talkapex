---
title: How to Find Matching Sets of Data Using SQL
tags:
  - sql
date: 2019-11-23 22:20:59
alias:
---


Suppose you have a set of data in Oracle, how can you find any other matching sets of data? This is different then most queries in SQL which just have "standard" predicates. For example most queries answer the question "*find me all cars that are red*" (`where color = 'red'`) whereas set comparison is answering the question "*find me all cars that are the same as a given set of cars*".

A good example of this problem is if you need to find orders that are **exactly** the same as a given order. An order can have multiple products in it. It's not as easy as comparing one row to another, rather sets of rows to other sets of rows. 

## Set Theory

Before continuing it's important to go over some basic set theory. One way to think of sets are as arrays of data. When comparing for equivalence of sets we need to ask two questions:

- Is everything in `A` in `B`? (i.e. `A - B = 0`)
- Is everything in `B` in `A`? (i.e. `B - A = 0`)

At first glance, the second point may seem unnecessary but it isn't as `A` may be a subset of `B`.

Take for example the following arrays:

- `A = 1,2,3`
- `B = 1,2,3,4`

In the above case, everything in `A` is in `B`, however everything in `B` (i.e. `4`) is not in `A`.


## Example

Suppose we have a table (`tab_a`) and we want to find any other tables with the exact same column definition.

```sql
-- Base table to be compared against
create table tab_a (
  id number,
  first_name varchar2(255),
  birth_date date
);

-- Close to tab_a but has an additional column (last_name)
create table tab_b (
  id number,
  first_name varchar2(255),
  last_name varchar2(255),
  birth_date date
);

-- Same as tab_a
create table tab_c (
  id number,
  first_name varchar2(255),
  birth_date date
);
```

The above script will create three tables. If we start with `tab_a` only `tab_c` is the exact same. Using the set theory logic above, and the [`minus`](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Set-Operators.html#GUID-5CB549AF-5A4F-453E-B164-49CAC8F94CBF) set operator clause in Oracle you can find matching tables using the following query:

```sql
select at_match.table_name
from dual
  -- Using the all_tables view so we have a large data set to compare against
  join all_tables at_src on 1=1
  join all_tables at_match on 1=1
    -- Exclude the same table
    and at_match.table_name != at_src.table_name
where 1=1
  and at_src.table_name = 'TAB_A'
  -- A - B = 0
  and not exists (
    select atc.column_name, atc.data_type
    from all_tab_columns atc
    where atc.table_name = at_src.table_name
    minus
    select atc.column_name, atc.data_type
    from all_tab_columns atc
    where atc.table_name = at_match.table_name
  )
  -- B - A = 0
  and not exists (
    select atc.column_name, atc.data_type
    from all_tab_columns atc
    where atc.table_name = at_match.table_name
    minus
    select atc.column_name, atc.data_type
    from all_tab_columns atc
    where atc.table_name = at_src.table_name
  )
;

TABLE_NAME
----------
TAB_C

```