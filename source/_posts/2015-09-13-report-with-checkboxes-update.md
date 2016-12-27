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

#### <span style="font-weight: normal;">Create Item to Hold List of IDs</span>

Create a hidden APEX item which will contain a comma delimited list of all the IDs that are to be checked off (in this example it will be <span style="font-family: Courier New, Courier, monospace;">P1_EMPNO_LIST</span>_)_. **Be sure to modify the _Value Protected_&nbsp;attribute to _No_**. This is critical as this item will be updated via AJAX and can not have any hashing/security applied to it.

If you are loading this from a cross reference table you can use the following query in a Before Header process. This query will load all the employees from the Accounting department.
<pre class="brush: sql;">select listagg(e.empno, ',') within group (order by e.empno)
from dept d, emp e
where 1=1
  and e.deptno = d.deptno
  and d.dname = 'ACCOUNTING'
</pre>

#### <span style="font-weight: normal;">Create IR Report with Checkboxes</span>
**
**Create an IR with the query below. Note the <span style="font-family: Courier New, Courier, monospace;">p_attributes</span>&nbsp;value. This is critical as we need to identify the checkboxes that should be monitored.
<pre class="brush: sql;">select
  apex_item.checkbox2(
    p_idx =&gt; 1,
    p_value =&gt; e.empno ,
    p_attributes =&gt; 'class="empno"',
    p_checked_values =&gt; :p1_empno_list,
    p_checked_values_delimiter =&gt; ',') checkbox,
  e.ename,
  e.job
from emp e
</pre>In the report attributes set the _Page Items to Submit_&nbsp;to&nbsp;<span style="font-family: Courier New, Courier, monospace;">P1_EMPNO_LIST</span>. Each time the report is refreshed (pagination, filters, sorting, etc) the active list of selected values will be submitted.

#### <span style="font-weight: normal;">Create Dynamic Action (DA)</span>
<div>
</div><div>Create a new DA with the attributes in the below image. This DA will append/remove the comma delimited list of IDs in <span style="font-family: Courier New, Courier, monospace;">P1_EMPNO_LIST</span>. Note the _jQuery Selector_ value must match what was used in the IR query above.</div><div>
</div><div><div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-xJNvFChTWxU/VfYM4-26OgI/AAAAAAABGdQ/17JvBPLA1NY/s320/Snip20150913_1.png)](http://1.bp.blogspot.com/-xJNvFChTWxU/VfYM4-26OgI/AAAAAAABGdQ/17JvBPLA1NY/s1600/Snip20150913_1.png)</div>
</div><div>Configure a True action as shown below (JS code follows).</div><div>
</div><div><div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-ds8NWT305ks/VfYM5KF861I/AAAAAAABGdc/PrMHN9c60pk/s400/Snip20150913_3.png)](http://1.bp.blogspot.com/-ds8NWT305ks/VfYM5KF861I/AAAAAAABGdc/PrMHN9c60pk/s1600/Snip20150913_3.png)</div></div><div><pre class="brush: js;">var
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
</pre>
</div>

#### <span style="font-weight: normal;">Demo</span>
<div>
</div><div>That's all that's required. Now each time a checkbox is checked/unchecked <span style="font-family: Courier New, Courier, monospace;">P1_EMPNO_LIST</span>&nbsp;will be updated to reflect these changes. The checkboxes will persist each time the report is refreshed. You can see the checkbox implantation in [this demo](https://apex.oracle.com/pls/apex/f?p=16406:1400).</div><div>
</div>

#### <span style="font-weight: normal;">Considerations</span>
<div>
</div><div>This solution is fairly simple to create an manage however it does have one small caveat. If the list of checked items is very large (more than 4000 characters) you may run into some <span style="font-family: Courier New, Courier, monospace;">varchar2</span> issues. In most cases this shouldn't be an issue but if it is you should test first.</div><div>
</div><div>To process the list of comma delimited list you can use the <span style="font-family: Courier New, Courier, monospace;">[apex_util.string_to_table](https://docs.oracle.com/cd/E59726_01/doc.50/e39149/apex_util.htm#AEAPI185)</span> function and loop over the table values. If you want to use the comma delimited list in a query the following example should work (again it does have <span style="font-family: Courier New, Courier, monospace;">varchar2</span> size limitations).</div><pre class="brush: sql;">select regexp_substr(:p1_empno_list,'[^,]+', 1, level) empno
from dual
connect by regexp_substr(:p1_empno_list, '[^,]+', 1, level) is not null
</pre>
