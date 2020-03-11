---
title: Conditional UPDATE Triggers in Oracle
tags:
  - plsql
date: 2020-03-04 07:00:00
alias:
---


In Oracle, most triggers are defined similar to the snippet below:

```sql
create or replace trigger trigger_name
  before insert or update or delete
  on my_table for each row
begin
...
```

*All demos below reference the following table:*

```sql
create table demo_table(col_x varchar2(10), col_y varchar2(10));
insert into demo_table values('hello', 'world');
commit;
```

They're some cases where you only want the trigger to be run when updating specific columns. They're various ways to do this which are shown below.

## In Trigger Definition (`update of ...`)

Using the `update of ` clause you can explicitly list the columns that will execute the trigger when updated.

```sql
create or replace trigger demo_trigger
  before update of col_x
  on demo_table for each row
begin
  dbms_output.put_line('***COL_X*** is being updated: ' || :new.col_x);
end;
/

set serveroutput on

-- Update another column
update demo_table
set col_y = 'world';

1 row updated.

-- Note: trigger wasn't run as it's not in the list of columns defined in "update of ..."


-- Update the "triggered" column
update demo_table
set col_x = 'goodby';

-- Note: This output shows the trigger was executed
***COL_X*** is being updated: goodby

1 row updated.
```


## Checking Column (`updating('col_name')`)

In this example, the trigger will run for all `update` statements and explicit checks can be added to see if a given column is being updated.

```sql
create or replace trigger demo_trigger
  before update
  on demo_table for each row
begin

  dbms_output.put_line('Trigger started');

  if updating('col_x') then
    dbms_output.put_line('***COL_X*** is being updated: ' || :new.col_x);
  end if;
end;
/

-- Running the same examples as first demo
set serveroutput on

-- Update another column
update demo_table
set col_y = 'world';

Trigger started

1 row updated.

-- Note: trigger was run (as shown by "Trigger Started" message) but COL_X wasn't updated

-- Update the "triggered" column
update demo_table
set col_x = 'goodby';

Trigger started
***COL_X*** is being updated: goodby

1 row updated.

-- Trigger was run and COL_X was updated
```

I recommend reading the Oracle documentation on [Conditional Predicates for Detecting Triggering DML Statement](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/lnpls/plsql-triggers.html#GUID-EC6A8FA1-9E60-4374-9905-639F4F100D83)
