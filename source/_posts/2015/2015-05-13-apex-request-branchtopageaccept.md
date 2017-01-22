---
title: 'APEX Request: BRANCH_TO_PAGE_ACCEPT'
tags:
  - apex
date: 2015-05-13 06:30:00
alias:
---

In APEX, there's a special request type called [BRANCH_TO_PAGE_ACCEPT](https://docs.oracle.com/cd/E59726_01/doc.50/e39147/concept_sub.htm#CHDICEDJ). This can be used in the REQUEST portion of the [APEX URL](http://docs.oracle.com/cd/E37097_01/doc.42/e35125/concept_url.htm#BEIFCDGF). The syntax for the `REQUEST` attribute is `BRANCH_TO_PAGE_ACCEPT|<branch_page_request>`.

For example, suppose you are on Page 1 (P1) and want a button which will:
- Go Page 2 (P2)
- Set `P2_X` to `some_value`
- Automatically submit the page, simulating the SAVE button on P2

Create a button on P1 that redirects to the following URL: `f?p=&APP_ID.:2:&APP_SESSION.:BRANCH_TO_PAGE_ACCEPT|SAVE:&DEBUG.:2:P2_X:some_value`

The [current documentation](https://docs.oracle.com/cd/E59726_01/doc.50/e39147/concept_sub.htm#CHDICEDJ) for `BRANCH_TO_PAGE_ACCEPT` states that "_Using BRANCH_TO_PAGE_ACCEPT is the same as navigating to page 1, entering a value into the item P1_DATA, and clicking a button that submits the page with a SAVE request._" You may interpret this as:

- Run Page 2
  - Therefor load/run the page rendering regions and processes
- Simulate clicking the Save button on P2
- Run the Page Processing of P2

This is not entirely true as the following occurs when a user goes to the above URL:

- Set `P2_X` to `some_value` (this is regular APEX URL behavior)
- Execute the Page Processing portion of P2.
  - This includes Validations, Processes, and Page branching on P2 as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-b8W9rpVxcIE/VVIuOrN5w4I/AAAAAAAAHEY/ddRVI-TNSs8/s320/Snip20150512_4.png)](http://3.bp.blogspot.com/-b8W9rpVxcIE/VVIuOrN5w4I/AAAAAAAAHEY/ddRVI-TNSs8/s1600/Snip20150512_4.png)</div>

What does not occur is the Page Rendering potion of P2. This is important to note because if you had some Pre-Rendering page processes (i.e. on page load) they will not be run. This is slightly contrary to what the documentation states that it is "... _the same as navigating to Page 2 ..._".

If everything runs smoothly on the P2 submit page processing (i.e no errors or validations fail) then you will branch to the defined page branch on P2. If something does fail then you will be shown P2 along with the corresponding error message.

It is important to note that currently the page item built-in validations are not executed when using `BRANCH_TO_PAGE_ACCEPT`. For example, suppose that you set Value Required attribute to Yes for `P2_X`.

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-eFh4HT4VARI/VVIvFz3CVCI/AAAAAAAAHEg/0J6vgLH0t0g/s1600/Snip20150512_5.png)](http://1.bp.blogspot.com/-eFh4HT4VARI/VVIvFz3CVCI/AAAAAAAAHEg/0J6vgLH0t0g/s1600/Snip20150512_5.png)</div>

If you run P2 normally, set `P2_X` to an empty value, then submit the page, an error will appear stating that "_X must have some value_". This is done automatically for you due to the Value Required attribute. If you use the `URL` `f?p=&APP_ID.:2:&APP_SESSION.:BRANCH_TO_PAGE_ACCEPT|SAVE:&DEBUG.:2:P2_X:` it will set `P2_X` to `null`, no error will occur as the page item's build in validations aren't executed.
