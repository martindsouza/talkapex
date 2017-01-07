---
title: 'APEX 5: Creating Sub-Menus'
tags:
  - apex
  - apex-5
date: 2015-04-20 07:00:00
alias:
---

APEX 5 has gotten rid of the old APEX tabs concept and instead replaced it with the APEX Navigation Menu. This is a very positive change and, tied in with the new Universal Theme it, certainly makes life easier.

One thing that I really like is the simplicity to create sub-menus. They're various ways to create sub-menus. This post will cover creating sub-menus from both the page creation wizard and via the Shared Components &gt; Navigation Menu options. &nbsp;There will be three pages in this example: parent, child, and grand child.

To start, I'll assume that you have one page and one Navigation Menu item called Parent. Next, create a second page called Child using the wizard. In the wizard, configure the Navigation Menu options as shown below:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-8B0z_dq3pXQ/VTL-1uWIS3I/AAAAAAAAG_Q/KqGt79mUjnM/s1600/Snip20150418_2.png)](http://2.bp.blogspot.com/-8B0z_dq3pXQ/VTL-1uWIS3I/AAAAAAAAG_Q/KqGt79mUjnM/s1600/Snip20150418_2.png)</div><div class="separator" style="clear: both; text-align: center;">
</div><span id="goog_1516739891"></span>Finish the page creation wizard. When you run your app you'll now see the Parent/Child menu and sub-menu.
<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-D8fEfMZVY9Y/VTL_RA_xu3I/AAAAAAAAG_Y/74aKgvY9790/s1600/Snip20150418_3.png)](http://4.bp.blogspot.com/-D8fEfMZVY9Y/VTL_RA_xu3I/AAAAAAAAG_Y/74aKgvY9790/s1600/Snip20150418_3.png)</div><div class="separator" style="clear: both; text-align: left;">
</div><div class="separator" style="clear: both; text-align: left;">Create a third page, called Grand Child. This time when you get the the Navigation Menu wizard option you can't select the Child page as it's parent. It is shown in the list, but greyed out. To get around this issue, just select "No Parent Selected" and finish the wizard.&nbsp;</div><div class="separator" style="clear: both; text-align: left;">
</div><div class="separator" style="clear: both; text-align: left;">To make the Grand Child page a sub-menu of Child:</div><div class="separator" style="clear: both; text-align: left;"></div>

*   Go to Shared Components &gt; Navigation Menu.&nbsp;
*   Edit the Grant Child list entry.
*   Select Child for the&nbsp;Parent List Entry and click Apply Changes.<div>If you rerun the application you should now see the updated sub-menu structure.</div><div>
</div><div><div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-WaSM0ccyvUI/VTMBTjvBshI/AAAAAAAAG_s/uPx9NYZO428/s1600/Snip20150418_4.png)](http://2.bp.blogspot.com/-WaSM0ccyvUI/VTMBTjvBshI/AAAAAAAAG_s/uPx9NYZO428/s1600/Snip20150418_4.png)</div><span id="goog_9711346"></span><span id="goog_9711347"></span>
The above example show the default Navigation Menu with the new Universal Theme, which is a side menu. To make it into a top navigation menu with a drop down sub-menus:

*   Go to Shared Components &gt; User Interface Attributes.
*   Edit the Desktop theme.
*   Click on the Navigation Menu tab, and make the following changes:<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-mt3fMIKYbYc/VTMEPNBsX0I/AAAAAAAAG_4/dI1kqCb8e9Q/s1600/Snip20150418_5.png)](http://2.bp.blogspot.com/-mt3fMIKYbYc/VTMEPNBsX0I/AAAAAAAAG_4/dI1kqCb8e9Q/s1600/Snip20150418_5.png)</div><div>
</div><div>If you refresh the page the Navigation Menu will now be at the top of the page and has the drop down menu/sub-menu.</div><div>
</div><div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-kOsgVlZW3rs/VTMEnTRhmuI/AAAAAAAAHAA/dGlApSHwaX0/s1600/Snip20150418_6.png)](http://2.bp.blogspot.com/-kOsgVlZW3rs/VTMEnTRhmuI/AAAAAAAAHAA/dGlApSHwaX0/s1600/Snip20150418_6.png)</div><div>
</div></div>
