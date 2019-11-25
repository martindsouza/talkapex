---
title: Partial Rollbacks
tags:
  - plsql
date: 2017-04-27 04:28:41
alias:
---


The ability to rollback transactionsin PL/SQL is very helpful when something goes wrong and you want to _undo_ what was just done. At a very high level, most code that use `rollback` look like the following:

```sql
begin
  ...
exception
  when others then
    rollback;
    raise;
end;
```

The above code makes sense as the `rollback` occurs when an error happens. (_Note for APEX users, an implicit `rollback` occurs in processes if an exception occurs_) The caveat is that it rollsback the entire transaction (i.e. from the start). What if the code is part of a larger block of code and you only want to `rollback` to the previous step? The following pseudo code is an example:

```sql
begin
  do_step_1;
  do_step_2;
  -- Step 3 may error out and if it does, still want to preserve the work of steps 1 and 2.
  do_step_3;
  do_step_4;
end;
```

In `do_step_3` if a generic `rollback` was used in the exception block it would undo any chnages that were done in step's 1 and 2 which is not the desired outcome. Thankfully PL/SQL rollback functionality supports this by using `savepoint`. If we want `do_step_3` to work as intented this is what it should look like:

```sql
create or replace procedure do_step_3 as
begin
  savepoint start_step_3;

  ...

exception
  when some_expected_error then
    -- This will rollback all changes to the start of do_step_3
    rollback to savepoint start_step_3;
    -- no raise as this was an expected error
  when others then
    raise;
end do_step_3;
```

A few things to note about `savepoint`s:

- The `rollback to savepoint x` doesn't need to be called in the `exception` block. I've used it in other places as well. Ex: `if <> then rollback to savepoint...`
- Try not to litter your code with `savepoint`s. It can get very confusing and tough to debug if you have a lot of them. The business logic will really determine when you need to use them.
- In the past ten years I've only needed to use them a handful of times. It's not a common feature that you'll need to use but good to know when you need it.

Oracle documentation for `savepoint` can be found [here](http://docs.oracle.com/database/122/SQLRF/SAVEPOINT.htm#SQLRF01701).



