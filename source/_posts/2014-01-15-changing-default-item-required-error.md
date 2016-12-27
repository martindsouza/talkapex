---
title: Changing Default Item Required Error Message in APEX
tags:
  - APEX
date: 2014-01-15 07:00:00
alias:
---

In APEX, if a page item is a required value it is no longer necessary to create an explicit validation. Setting the item's option "Value Required" to Yes will make it a required field as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-wWvrItpfLJw/UspIv3bmXWI/AAAAAAAAEkc/IDVpsW405Ek/s1600/Edit-Page-Item.png)](http://4.bp.blogspot.com/-wWvrItpfLJw/UspIv3bmXWI/AAAAAAAAEkc/IDVpsW405Ek/s1600/Edit-Page-Item.png)</div>
This is much quicker then manually creating Not Null validations for each required item as required in older versions of APEX.

If the page is submitted and the item is null, then the page will be reloaded with a validation error message stating that "_<label>&nbsp;must have some value.</label>_" The default message can me changed by doing the following:

*   Go to Shared Components &gt; Text Messages (under Globalization)
*   Create Text Message<div>When the page is now submitted and the item is null, the page will be reloaded with a validation error message stating that "_<label> can not be null</label>_".&nbsp;</div><div>
</div><div>The above technique changes the default message for all items that have their Value Required option set to Yes (i.e. it's an application level setting).</div>