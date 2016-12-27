---
title: Custom Code for Tabular Forms (Part 2)
tags:
  - apex
  - apex-5
  - tabular-form
date: 2015-09-13 21:46:00
alias:
---

The [previous article](http://www.talkapex.com/2015/09/custom-code-for-tabular-forms-part-1.html) covered how to create a Tabular Form with manual code rather than automatic row processing. This article will demonstrate how to change the Tabular Form to modify data from multiple tables using the same Tabular Form.

### Modify Tabular Form

Using the example from the [previous article](http://www.talkapex.com/2015/09/custom-code-for-tabular-forms-part-1.html), modify the Tabular Form and change the query to:

```sql
select
  e.empno,
  e.empno empno_display,
  e.ename,
  e.sal,
  d.dname
from emp e, dept d
where 1=1
  and e.deptno = d.deptno
```

### Edit `DNAME`

Edit the newly added `DNAME` column with the following changes:
<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-cTlLNsZvGeU/VfY-iM4PCAI/AAAAAAABGeU/UkfeWjEPUSQ/s320/Snip20150819_8.png)](http://2.bp.blogspot.com/-cTlLNsZvGeU/VfY-iM4PCAI/AAAAAAABGeU/UkfeWjEPUSQ/s1600/Snip20150819_8.png)</div><div class="separator" style="clear: both; text-align: center;">
</div>

### Modify Page process
<span style="font-weight: normal;">Change the page process to use the code snippet below. _Note:&nbsp;_</span>_This isn't the best example, as the below code will update the department name for each modified employee record. It does highlight is that you can reference and modify data from multiple tables._

```sql
if :empno is null then
  -- code to insert emp
  null;
else
  update emp
  set
    ename = :ename,
    sal = :sal
  where empno = :empno;

  -- Update dept name
  update dept d
  set dname = :dname
  where 1=1
    and d.deptno = (
      select e.deptno
      from emp e
      where e.empno = :empno)
end if;
```

If you've ever developed a true manual tabular form using collections, the above approach covered in this article may be a better alternative to manage and maintain.
