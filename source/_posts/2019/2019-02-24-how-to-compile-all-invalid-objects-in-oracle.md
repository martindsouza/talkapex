---
title: How to Compile all Invalid Objects in Oracle
tags:
  - sql
  - plsql
date: 2019-02-24 22:55:41
alias:
---


When reviewing a team's release processes I commonly encounter developers spending a lot of time and concern over the order of their PL/SQL objects (i.e. views and packages). For example if `pkg_a` references `view_b` then the release looks like:

```sql
...
@pkg_a.pks
@pkg_a.pkb
@view_b.sql
...
```

At first glance this may not seem too bad but when working with larger applications or a high change rate of code (think initial development) this can take up a lot of wasted time. It can also get much more complicated when dealing with many packages and views to understand their dependencies to ensure that the code is compiled in the "correct order". Thankfully you don't need to worry about this with Oracle as there's an API to compile all the invalid objects:

```sql
begin
 dbms_utility.compile_schema(schema => user, compile_all => false);
end;
/
```

For all of this to work effectively ensure that all your views are written as `create or replace force view view_name` note the keyword `force`. This will ensure that the view is compiled even if it's invalid.

Using the `dbms_utility.compile_schema` procedure and leveraging the `force` in the `create view` statements, most of my releases look like the following:

```sql
-- views
@view_b.sql
...

-- packages
@pkg_a.pks
@pkg_a.pkb
...

-- Recompile invalid objects
begin
 dbms_utility.compile_schema(schema => user, compile_all => false);
end;
/
```

For very active development cycles (where a lot of the code is changing) I even have scripts to scrape both the `packages` and `views` folder and auto-inject all the file names into the release file. This way developers don't need to worry about which packages or views were modified for a given release (nor their order/dependencies) as they will all be re-compiled.