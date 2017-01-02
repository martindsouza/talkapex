---
title: Report with Checkboxes (an update)
tags:
  - apex
  - checkbox
  - tabular-form
date: 2015-09-13 20:40:00
alias:
---

Supposed you have a report with checkboxes. Once the user selects all the rows, they can submit the page and the application would process the rows. Sounds pretty simple and straight forward however they're some additional requirements:

- The report is an Interactive Report
- There may be up to 10,000 records in the report
- When the user "scrolls" through the report (i.e. uses pagination), if they checked off a box it should remain checked the entire time (i.e. if they check an row in the 1st 15 rows, then view rows 16~30, then go back to rows 1~15 it should remain checked). Same applies if a user uses the IR filters.

_If you follow this blog and the above sounds familiar, that's because it is. I [wrote about this problem](http://www.talkapex.com/2009/01/apex-report-with-checkboxes-advanced.html) a very long time ago. A lot has changed since 2009 and an update on the original post is long overdue. Here's the updated (and simplified) solution._
_
_

### Create Item to Hold List of IDs

Create a hidden APEX item which will contain a comma delimited list of all the IDs that are to be checked off (in this example it will be `P1_EMPNO_LIST`). **Be sure to modify the `Value Protected` attribute to `No`**. This is critical as this item will be updated via AJAX and can not have any hashing/security applied to it.

If you are loading this from a cross reference table you can use the following query in a Before Header process. This query will load all the employees from the Accounting department.
```sql
select listagg(e.empno, ',') within group (order by e.empno)
from dept d, emp e
where 1=1
  and e.deptno = d.deptno
  and d.dname = 'ACCOUNTING'
```

### Create IR Report with Checkboxes

Create an IR with the query below. Note the `p_attributes` value. This is critical as we need to identify the checkboxes that should be monitored.
```sql
select
  apex_item.checkbox2(
    p_idx =&gt; 1,
    p_value =&gt; e.empno ,
    p_attributes =&gt; 'class="empno"',
    p_checked_values =&gt; :p1_empno_list,
    p_checked_values_delimiter =&gt; ',') checkbox,
  e.ename,
  e.job
from emp e
```

In the report attributes set the `Page Items to Submit` to `P1_EMPNO_LIST`. Each time the report is refreshed (pagination, filters, sorting, etc) the active list of selected values will be submitted.

### Create Dynamic Action (DA

Create a new DA with the attributes in the below image. This DA will append/remove the comma delimited list of IDs in `P1_EMPNO_LIST`. Note the `jQuery Selector` value must match what was used in the IR query above.

[![](http://1.bp.blogspot.com/-xJNvFChTWxU/VfYM4-26OgI/AAAAAAABGdQ/17JvBPLA1NY/s320/Snip20150913_1.png)](http://1.bp.blogspot.com/-xJNvFChTWxU/VfYM4-26OgI/AAAAAAABGdQ/17JvBPLA1NY/s1600/Snip20150913_1.png)

Configure a True action as shown below (JS code follows).

[![](http://1.bp.blogspot.com/-ds8NWT305ks/VfYM5KF861I/AAAAAAABGdc/PrMHN9c60pk/s400/Snip20150913_3.png)](http://1.bp.blogspot.com/-ds8NWT305ks/VfYM5KF861I/AAAAAAABGdc/PrMHN9c60pk/s1600/Snip20150913_3.png)
```js
var
  //Checkbox that was changed
  $checkBox = $(this.triggeringElement),
  //DOM object for APEX Item that holds list.
  apexItemIDList = apex.item(this.affectedElements.get(0)),
  //Convert comma list into an array or blank array
  //Note: Not sure about the "?" syntax see: http://www.talkapex.com/2009/07/javascript-if-else.html
  ids = apexItemIDList.getValue().length === 0 ? [] : apexItemIDList.getValue().split(','),
  //Index of current ID. If it's not in array, value will be -1
  idIndex = ids.indexOf($checkBox.val())
;

//If box is checked and it doesn't already exist in list
if ($checkBox.is(':checked') && idIndex < 0) {
  ids.push($checkBox.val());
}
//If box is unchecked and it exists in list
else if (!$checkBox.is(':checked') && idIndex >= 0){
  ids.splice(idIndex, 1);
}

//Convert array back to comma delimited list
apexItemIDList.setValue(ids.join(','));
```


### Demo

That's all that's required. Each time a checkbox is checked/unchecked `P1_EMPNO_LIST` will be updated to reflect these changes. The checkboxes will persist each time the report is refreshed. You can see the checkbox implantation in [this demo](https://apex.oracle.com/pls/apex/f?p=16406:1400).


### Considerations

This solution is fairly simple to create an manage however it does have one small caveat. If the list of checked items is very large (more than 4000 characters) you may run into some `varchar2` issues. In most cases this shouldn't be an issue but if it is you should test first.
To process the list of comma delimited list you can use the [`apex_util.string_to_table`](https://docs.oracle.com/cd/E59726_01/doc.50/e39149/apex_util.htm#AEAPI185) function and loop over the table values. If you want to use the comma delimited list in a query the following example should work (again it does have `varchar2` size limitations).

```sql
select regexp_substr(:p1_empno_list,'[^,]+', 1, level) empno
from dual
connect by regexp_substr(:p1_empno_list, '[^,]+', 1, level) is not null
```
