---
title: APEX 19 Form PL/SQL Processing
tags:
  - apex-19
date: 2020-03-13 07:00:00
alias:
---


Starting with APEX 19.1 a new form type was introduced that replaces the old "Auto DML" model with  much more robust functionality. [Carsten Czarski](https://twitter.com/cczarski) wrote an in depth [article](https://blogs.oracle.com/apex/forms-in-apex-191-more-power%2c-more-flexibility) about the new form regions and I suggest you read it before continuing with this post.

One really nice feature with the new form regions is the ability to have a PL/SQL process instead of automatic DML to save changes. This allows for all the great functionality to load data and manage lost updates, while having the customization of PL/SQL to process your data. An example of a Form Region with PL/SQL Code:

{% asset_img plsql-process.png %}

*Note the PL/SQL code sample was taken from the help section for demo purposes.*

Processes associated to a Form Region will only run if a value directly associated to that Form Region has changed. For example in the screen shot below, `P4_SAL` is associated with the `Emp` Form Region. If this value is changed than the process, `Process form Emp` (from the previous screen shot), will be executed.

{% asset_img page-item-sal.png %}

If a field is not associated with the Form Region and it's changed then the process associated to that Form Region will not run. For example, suppose a new page item is added: `P4_SAL_INCREASE`. It is not associated to any Form Region: 

{% asset_img page-item-sal-increase.png %}

The Form Region PL/SQL Processing code is then changed to:

```sql
...
  -- Updating
  when 'U' then
    update emp
    set sal  = :SAL + nvl(:P4_SAL_INCREASE, 0),
    ...
    where rowid  = :ROWID;
...
```

If a user only updates `P4_SAL_INCREASE` then the page process `Process form Emp` (i.e. the process associated to the Form Region) will not run since none of the associated values have changed.

They're several work arounds to this, each have their pros and cons:

- Create a computation or PL/SQL process (*that runs before the current process*) to set `:P4_SAL := :P4_SAL + nvl(:P4_SAL_INCREASE, 0)`. This will change the associate `P4_SAL` if `P4_SAL_INCREASE` has a value and therefore trigger the Form Region process to run.
- Change the process type to `PL/SQL Code` (no associated Form Region). This will make things very straight forward but the built-in row locking and lost update prevention functionalities will be lost.