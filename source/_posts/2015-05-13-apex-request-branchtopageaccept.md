---
title: 'APEX Request: BRANCH_TO_PAGE_ACCEPT'
tags:
  - APEX
date: 2015-05-13 06:30:00
alias:
---

In APEX, there's a special request type called [BRANCH_TO_PAGE_ACCEPT](https://docs.oracle.com/cd/E59726_01/doc.50/e39147/concept_sub.htm#CHDICEDJ). This can be used in the REQUEST portion of the [APEX URL](http://docs.oracle.com/cd/E37097_01/doc.42/e35125/concept_url.htm#BEIFCDGF). The syntax for the <span style="font-family: inherit;">REQUEST</span> attribute is "<span style="font-family: Courier New, Courier, monospace;">BRANCH_TO_PAGE_ACCEPT|<branch_page_request></branch_page_request></span>".

For example, suppose you are on Page 1 (P1) and want a button which will:
&nbsp; - Go Page 2 (P2)
&nbsp; - Set P2_X to _some_value_
&nbsp; - Automatically submit the page, simulating the SAVE button on P2

Create a button on P1 that redirects to the following URL: f<span style="font-family: Courier New, Courier, monospace;">?p=&amp;APP_ID.:2:&amp;APP_SESSION.:**<span style="color: red;">BRANCH_TO_PAGE_ACCEPT|SAVE</span>**:&amp;DEBUG.:2:P2_X:some_value</span>

The [current documentation](https://docs.oracle.com/cd/E59726_01/doc.50/e39147/concept_sub.htm#CHDICEDJ) for BRANCH_TO_PAGE_ACCEPT states that "_Using BRANCH_TO_PAGE_ACCEPT is the same as navigating to page 1, entering a value into the item P1_DATA, and clicking a button that submits the page with a SAVE request._" You may interpret this as:

&nbsp; - Run Page 2
&nbsp; &nbsp; - Therefor load/run the page rendering regions and processes
&nbsp; - Simulate clicking the Save button on P2
&nbsp; - Run the Page Processing of P2

This is not entirely true as the following occurs when a user goes to the above URL:

&nbsp; - Set P2_X to _some_value_ (this is regular APEX URL behaviour)
&nbsp; - Execute the Page Processing portion of P2.
&nbsp; &nbsp; - This includes Validations, Processes, and Page branching on P2 as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-b8W9rpVxcIE/VVIuOrN5w4I/AAAAAAAAHEY/ddRVI-TNSs8/s320/Snip20150512_4.png)](http://3.bp.blogspot.com/-b8W9rpVxcIE/VVIuOrN5w4I/AAAAAAAAHEY/ddRVI-TNSs8/s1600/Snip20150512_4.png)</div>

What does not occur is the Page Rendering potion of P2\. This is important to note because if you had some Pre-Rendering page processes (i.e. on page load) they will not be run. This is slightly contrary to what the documentation states that it is "...&nbsp;_the same as navigating to Page 2 ..._".

If everything runs smoothly on the P2 submit page processing (i.e no errors or validations fail) then you will branch to the defined page branch on P2\. If something does fail then you will be shown P2 along with the corresponding error message.

It is important to note that currently the page item built-in validations are not executed when using BRANCH_TO_PAGE_ACCEPT. For example, suppose that you set Value Required attribute to Yes for <span style="font-family: inherit;">P2_X</span>. 

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-eFh4HT4VARI/VVIvFz3CVCI/AAAAAAAAHEg/0J6vgLH0t0g/s1600/Snip20150512_5.png)](http://1.bp.blogspot.com/-eFh4HT4VARI/VVIvFz3CVCI/AAAAAAAAHEg/0J6vgLH0t0g/s1600/Snip20150512_5.png)</div>

If you run P2 normally, set P2_X to an empty value, then submit the page, an error will appear stating that "_X must have some value_". This is done automatically for you due to the Value Required attribute. If you use the <span style="font-family: inherit;">URL</span><span style="font-family: Courier New, Courier, monospace;"> f?p=&amp;APP_ID.:2:&amp;APP_SESSION.:**BRANCH_TO_PAGE_ACCEPT|SAVE**:&amp;DEBUG.:2:P2_X:</span>&nbsp;it will set P2_X to null, no error will occur as the page item's build in validations aren't executed.