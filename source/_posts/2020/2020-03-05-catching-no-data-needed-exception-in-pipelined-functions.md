---
title: Catching NO_DATA_NEEDED Exception in Pipelined Functions
tags:
  - plsql
date: 2020-03-05 07:00:00
alias:
---




[Pipelined Functions](https://oracle-base.com/articles/misc/pipelined-table-functions) are functions whose output is referenced as a table in a SQL statement. Here's an example of a simple pipelined function:

```sql
-- Prefix: Create supporting objects
create or replace type emp_info as object (
  ename varchar2(255),
  job varchar2(255)
);
/

create or replace type emp_info_arr as table of emp_info;
/

-- Pipelined Function (with error handling)
create or replace function f_pipeline_demo
  return emp_info_arr pipelined
as
  l_scope logger_logs.scope%type := lower($$plsql_unit);
begin
  for x in (
    select ename, job
    from emp
  ) loop
    pipe row (emp_info(x.ename, x.job));
  end loop;

exception
  when others then
    logger.log_error('Unhandled Exception', l_scope); 
end;
/


-- Query First 5 rows of Pipelined Function (they're 14 ros in emp table)
select *
from table(f_pipeline_demo)
where rownum <= 5
;

   ENAME          JOB
________ ____________
KING     PRESIDENT
BLAKE    MANAGER
CLARK    MANAGER
JONES    MANAGER
SCOTT    ANALYST
```

The above demo serves two purposes. First to provide a basic example of a pipelined function and second to show a "hidden error" in its implementation. No errors were raised when querying the pipelined function, however looking at the logger output shows that an error was raised:

```sql
select logger_level, text
from logger_logs_5_min
where 1=1
  and scope like 'f_pipeline_demo'
;

 LOGGER_LEVEL  TEXT  
 ____________  ____
            2  Unhandled Exception ORA-06548: no more rows needed ...
```

Taking a step back, the `emp` table has 14 rows in it. Querying `f_pipeline_demo` was limited to the first 5 rows by adding `where rownum <= 5`. When a pipelined function is called and it realizes that it no longer needs to process additional rows it stops by raising the [`no_data_needed`](https://docs.oracle.com/en/database/oracle/oracle-database/19/lnpls/plsql-optimization-and-tuning.html#GUID-4B0E1C6B-8C73-4905-9E97-37C2667D2184).

At first glance this may seem like a weird/bad outcome but it makes sense. Pipelined functions sometimes contain some costly computations in each loop. If only 5 rows are returned, doing the additional computations isn't required.

Since I log all exceptions in my code, I don't want to log `no_data_needed` exceptions as it's not a "bad" exception. To get around this I can simply change the exception block in the pipelined function to:

```sql

...
exception
  -- Make sure this is before the "when others then" block
  when no_data_needed then
    null;
  when others then
    logger.log_error('Unhandled Exception', l_scope); 
end;
/
```

Now when the pipelined function is queried with a restricted set of rows the `no_data_needed` exception won't be logged.