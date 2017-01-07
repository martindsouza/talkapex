---
title: Displaying Percentage Bar in APEX Reports
tags:
  - apex
date: 2009-06-01 09:00:00
alias:
---

APEX can create "Percentage Bars" within a report. They're probably a lot of 3rd party tools you can use for fancy percentage bars, however if you want a basic display to the user here's a quick way to do it. Click [here](http://apex.oracle.com/pls/otn/f?p=20195:1600) for a demo.

[![](http://3.bp.blogspot.com/_33EF80fk9sM/Sh9QMdwQ5PI/AAAAAAAADpI/cnJJTKr2r38/s400/22_pct_bar.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/Sh9QMdwQ5PI/AAAAAAAADpI/cnJJTKr2r38/s1600-h/22_pct_bar.bmp)

<span style="font-weight: bold">1- Create your report</span>
<span style="font-style: italic">In this report we're using the employees percentage of salary within their department</span>

```sql
SELECT e.ename,
       e.job,
       d.dname,
       e.sal,
       ROUND (e.sal / SUM (e.sal) OVER (PARTITION BY e.deptno) * 100, 0) pct_dep_sal,
       ROUND (e.sal / SUM (e.sal) OVER (PARTITION BY e.deptno) * 100, 0) bar
  FROM emp e,
       dept d
 WHERE e.deptno = d.deptno
```

<span style="font-weight: bold">2- Add Percentage Bar for the "bar" column</span>
- In the Reports Attributes section, click on the "Bar" column attributes
- Under Number/Date formatting enter the following: `PCT_GRAPH:330099:CC0000:100`

`PCT_GRAPH:<Hex background color>:<Hex foreground color>:<Bar width in pixels>`
