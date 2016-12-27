---
title: And the Winner Is...
tags:
  - meta
date: 2013-03-24 23:26:00
alias:
---

Two weeks ago I posted a question/contest for [Who's Birthday is it in "n" number of days?](http://www.talkapex.com/2013/03/whos-birthday-is-it-in-n-number-of-days.html) I received 63 answers (within the time limit) all with some interesting and unique approaches. Here's how I went about picking the winners:  **&nbsp;**

**Leap Year**

One thing that most people got wrong was what happened if the hire date was on Feb 29th (during a leap year) while the current year did not have a leap year. I could do a search online to find some recent leap years, instead I decided to wrote a query to find recent leap years.
<pre class="brush: sql;">-- Find past leap years
-- https://forums.oracle.com/forums/thread.jspa?threadID=1128019
select yr,
  case
    when mod(yr,400) = 0 or mod(yr,4) = 0then 'Leap Year'
    else null
  end leap
from (
  select extract (year from add_months(sysdate, -8*12)) + level - 1 yr
  from dual
  connect by level &lt;= 8
);
</pre>**Test Data**

Instead of testing against the main sample_ _EMP table I recreated the EMP table with all the dates in a given leap year (2008). I kept the same structure as the EMP table so that it would be easy to test your solutions against.
<pre class="brush: sql;">drop table emp;

-- create mock emp table with all the dates (including leap year
create table emp as
select level empno, level ename, level job, level mgr, to_date('01-01-2008', 'DD-MM-YYYY') + level - 1 hiredate, level sal, level comm, level deptno
from dual
connect by level &lt;= 366;
</pre>**Picking the winner and testing solution:**

Since we had 63 entries I wasn't going to test them all. Instead I decided to randomly pick solutions (based on the entry number) and validate their solution. If they were correct I'd mark them as a winner. If not, I'd move on to the next entry until I found 2 winners.

Here's how I picked the random entries:
<pre class="brush: sql;">-- Chose random winners
select trunc(dbms_random.value(1,63))
from dual;
</pre>To test the solutions I set my current <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">SYSDATE</span> to 1-Feb-2013 so that it would have to encounter people that were hired on 29-Feb (even though 29-Feb doesn't exist in 2013)**.**
<pre class="brush: sql;">ALTER SYSTEM SET fixed_date='2013-02-01-14:10:00';
</pre>**Winners**

The winners are _Erik van Roon (aka "Belly")_ and _Iudith Mentzel_. Please email me (my email address is top right corner of blog) and I will forward your information to [Red Gate](http://www.red-gate.com/) so that you can claim your prize.

Thanks for all the entries and thanks again to [Red Gate](http://www.red-gate.com/) for offering up the prizes (licenses to their new product: [Source Control for Oracle](http://www.red-gate.com/products/oracle-development/source-control-for-oracle/)). If you haven't already seen what [Source Control for Oracle](http://www.red-gate.com/products/oracle-development/source-control-for-oracle/) is or want to download a free trial I encourage you to check it out.
