---
title: How to Select an Optional Value
tags:
  - plsql
date: 2020-01-22 13:21:52
alias:
---


Sometimes in your PL/SQL code you need to query a table which may or may not contain what you're looking for. This post will cover some different ways of doing it.

## The problem

Suppose you need to find an employee's ID named `SAM`. If he doesn't exist in the table then insert a new employee record.

*Side note: Technically you can use a `merge` statement for this functionality but want to highlight that a row may not be found.*

```sql
declare
  l_empno emp.empno%type;
begin
  select e.empno
  into l_empno
  from emp e
  where e.ename = 'SAM';

  if l_empno is null then
    -- ... insert new emp
  end if;

  -- ...
end;
/

-- Errors
ORA-01403: no data found
ORA-06512: at line 4
1.     00000 -  "no data found"

```

An error is raised if the employee (`SAM` in this case) isn't found. Here are some options to get around this.

## Explicit exception block

This is the most correct way to know if the value exists or not. Some don't like to use this approach as it can clutter code (due to additional lines of code), on the positive side it is very explicit.

```sql
declare
  l_empno emp.empno%type;
begin

  begin
    select e.empno
    into l_empno
    from emp e
    where e.ename = 'SAM';
  exception
    when no_data_found then 
      l_empno := null;
  end;
  
end;
/
```

## Using `max`

Using an aggregate function will always return a value even if no rows are returned. For example:

```sql
select nvl(max(dummy), 'still returns value') val
from dual
where 1=2 -- Always false
;

VAL          
-------------
still returns value
```

Using this logic with the base example we can do:

```sql
declare
  l_empno emp.empno%type;
begin
  
  select max(e.empno)
  into l_empno
  from emp e
  where e.ename = 'SAM';
  
end;
/
```

I've used this in the past, however it comes with a caveat. If used on a column that isn't unique it will still return one value. If multiple values are present (ex: all `emp`s who's `job` is `MANAGER`) it can lead to false positives.

## Using `left join`

Similar to the previous option, selecting from `dual` (always returns a row) we can `left join` to optionally find the value.

```sql
declare
  l_empno emp.empno%type;
begin
  
  select e.empno
  into l_empno
  from dual
    left join emp e on 1=1
      and e.ename = 'SAM';
    
end;
/
```

I prefer this approach compared to the `max` approach since if multiple values are possible an error is returned (which in most cases we want):

```sql
declare
  l_empno emp.empno%type;
begin
  select e.empno
  into l_empno
  from dual
    left join emp e on 1=1
     and e.job = 'MANAGER' -- Will return multiple values
  ;   
end;
/

-- Error
ORA-01422: exact fetch returns more than requested number of rows
ORA-06512: at line 5
```




