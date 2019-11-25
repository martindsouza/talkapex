---
title: How to Refresh a Report Only Once With Multiple Dependant Cascading LOVs
tags:
  - apex
  - javascript
date: 2019-03-26 10:15:46
alias:
---


Suppose you have a region with a set of cascading LOVs and report that references both of these items. When either of these items is refreshed, the report should be refreshed. When the parent item is changed it will trigger multiple refreshes of the report. The following example highlights this problem:

{% asset_img overview.png %}

**p2_deptno:** 

```sql
select d.dname d, d.deptno r
from dept d
order by dname
```

**p2_empno:**

```sql
select e.ename d, e.empno r
from emp e
where 1=1
  and e.deptno = nvl(:p2_deptno, e.deptno)
order by e.ename
```

_Cascading LOV Parent Item(s)_: `p2_deptno`

**Report Region**

_Type_: `Classic Report`

_SQL Query_:

```sql
select empno, ename, deptno
from emp e
where 1=1
  and e.deptno = nvl(:p2_deptno, e.deptno)
  and e.empno = nvl(:p2_empno, e.empno)
```

_Page Items to Submit_: `p2_deptno,p2_empno`


Next, create a Dynamic Action (DA) that will trigger a refresh on the `Report` region each time any item has changed:

{% asset_img dynamic-action.png %}

When `p2_deptno` is changed, 3 Ajax requests occur:

1. `Report` region is refreshed (reason: Page items to Submit contains `p2_deptno`)
1. `p2_empno` LOV is refreshed (reason: Cascading LOV Parent Item(s): `p2_deptno`). This triggers another refresh to the `Report` region (see next step)
1. `Report` region is refreshed (reason: Page items to Submit contains `p2_empno`)

The following demo highlights this problem. The report refreshes so quickly the only way to see the problem is to look at the differnet Ajax calls. _Note: the order of the Ajax responses may change since it's asynchronous._

{% asset_img cascade-lov-dup.gif %}

If your report runs relatively quick you may not even notice the duplicate refresh. I had a situation where we had five cascading LOVs and the underlying report was refreshed five times and was very noticeable to the users.

Thankfully there's a simple fix to this problem. When a JavaScript (JS) `change` event occurs and is triggered "by a user" the JS event `e.originalEvent` contains a value. In the above example step 3 was not really triggered by a user, rather triggered by a cascading LOV. In this case the JS event attribute `originalEvent` is null.

Using the event attribute `originalEvent` we can solve the problem by adding the following Client-side Condition to the DA: `this.browserEvent.originalEvent !== undefined`

{% asset_img da-condition.png %}

Now when `p2_deptno` is modified only two Ajax calls occur (which is correct):

{% asset_img cascade-lov-fix.gif %}

Thanks to [Adrian Png](https://twitter.com/fuzziebrain) for helping me with this.