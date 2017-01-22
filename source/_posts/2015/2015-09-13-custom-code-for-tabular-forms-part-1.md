---
title: Custom Code for Tabular Forms (Part 1)
tags:
  - apex
  - apex-5
  - tabular-form
date: 2015-09-13 21:44:00
alias:
---

_Over a year ago I [wrote an article](http://www.talkapex.com/2014/03/running-custom-code-for-tabular-forms.html) covering how to create a tabular form and then use custom PL/SQL to process the data rather than the automatic Apply MRU and Apply MRD processes. The demo showed how to do this in APEX 4.2. This article will re-introduce the topic but use APEX 5.0 instead._

### Create new Tabular Form

*   Create page > Page type: Form > Tabular Form
*   Select the following options:
<div class="separator" style="clear: both; text-align: center;"></div><div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-duB7vyTGl6E/VfY-h4brEsI/AAAAAAABGeQ/0RUqdyYs4nY/s400/Snip20150819_4.png)](http://2.bp.blogspot.com/-duB7vyTGl6E/VfY-h4brEsI/AAAAAAABGeQ/0RUqdyYs4nY/s1600/Snip20150819_4.png)</div>

*   Note: For simplicity/demo purposes, limiting to just the `SAL` and `ENAME` columns.*   Primary Key: Select Primary Key Column(s) > Primary Key Column 1 > `EMPNO`
*   Run through the rest of the wizard.

### Remove Automatic DML

Since custom code will be used to process the page, delete the automatic row processing process as shown below.
<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-XLOIS1KKP5U/VfY-hof08_I/AAAAAAABGeM/EmC7fnQ2NwU/s400/Snip20150819_5.png)](http://3.bp.blogspot.com/-XLOIS1KKP5U/VfY-hof08_I/AAAAAAABGeM/EmC7fnQ2NwU/s1600/Snip20150819_5.png)</div>

### Create Process
Create a new process with the following settings (the _Source_ is included below the image).

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-uN4L578OnPE/VfY-iIY8VTI/AAAAAAABGec/Eabvar2pKx0/s400/Snip20150819_7.png)](http://2.bp.blogspot.com/-uN4L578OnPE/VfY-iIY8VTI/AAAAAAABGec/Eabvar2pKx0/s1600/Snip20150819_7.png)</div>

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
end if;
```

Using the above technique you can now use a tabular form to call custom PL/SQL code rather than the automatic row processing.

The [next article](http://www.talkapex.com/2015/09/custom-code-for-tabular-forms-part-2.html) will cover how to modify data from multiple tables in the same Tabular Form.
