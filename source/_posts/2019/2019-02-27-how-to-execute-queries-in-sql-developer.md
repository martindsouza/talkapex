---
title: How to Execute Queries in SQL Developer
tags:
  - sql-developer
date: 2019-02-27 21:12:55
alias:
---


They're certain things in life that absolutely drive me crazy. I can probably start an entire blog on this topic but instead will focus on something I see many developers do in [SQL Developer](https://www.oracle.com/database/technologies/appdev/sql-developer.html).

Unfortunately I've see a lot of developers run their queries using a mouse instead of a keyboard shortcut (note no semicolon between queries) as demonstrated below:

{% asset_img sqldev-inefficient.gif %}

You'll notice that my second query had an error with it so I had to select it again, then click the run button to execute it.

At first glance this may not seem like a big deal however if you run hundreds of queries a day that are much longer than two lines the inefficiencies really start to add up. With the use of the right syntax you can execute everything in SQL Developer via keyboard shortcuts. In the example below everything is run using `ctrl+enter`

{% asset_img sqldev-correct.gif %}

The syntax structure that you need to use is very simple:

- After every query have a semicolon (`;`)
- After every PL/SQL block end with a semicolon (`;`) and then a forward slash (`/`)

The following code sample is what was used in the second demo:

```sql
select *
from emp
;

select *
from dept
;

begin
  dbms_output.put_line('hello');
end;
/
```




