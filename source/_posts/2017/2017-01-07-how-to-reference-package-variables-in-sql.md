---
title: How to Reference Package Variables in SQL
tags:
  - sql
  - plsql
  - oracle-12c
date: 2017-01-07 12:40:27
alias:
---


A long time ago (pre-12c) I wrote about {% post_link how-to-reference-package-variables %}. This technique used an `execute immediate` function to reference a given package variable. Another alternative at the time was to create individual `get` functions for each variable.

Starting in Oracle 12c you can directly use PL/SQL in SQL and thus reference package variables in SQL. The following example shows how:


```sql
create or replace package pkg_demo as
  gc_first_name constant varchar2(255) := 'Martin';
end pkg_demo;
/

with
  function get_name return varchar2 as
  begin
    return pkg_demo.gc_first_name;
  end;
select get_name() my_name
from dual;
/

-- Will return
MY_NAME
Martin
```

You can also use this concept in views.
