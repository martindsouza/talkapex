---
title: Moving the APEX Developer Toolbar
tags:
  - apex
date: 2011-12-08 07:00:00
alias:
---

When you are developing an APEX application there may be certain situations (primarily depending on your page template) that require you to move the APEX toolbar from the bottom of the screen. The screen shot below shows such a case where there is content all the way to the end of the screen and the toolbar is preventing you from accessing/clicking on something at the bottom. If you try to click the last Edit button nothing happens since the toolbar is overlaying it. _Note: I modified the page template to highlight this issue._

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-0g2ayhW6PWU/Ttpjlmd51TI/AAAAAAAAEFw/9rO2p4y4UAk/s400/toolbar_issue.png)](http://1.bp.blogspot.com/-0g2ayhW6PWU/Ttpjlmd51TI/AAAAAAAAEFw/9rO2p4y4UAk/s1600/toolbar_issue.png)</div>
This doesn't occur very often but when it does there's a simple solution to move the toolbar from the bottom to the top of the screen. Run the following JavaScript code in your browser's console window and the toolbar will move to the top of the page.
<pre class="brush: js">//Note on older versions of APEX you'll need to check the toolbar's ID
$('#apex-dev-toolbar').css({
  top: 0,
  height: '22px'
});
</pre>Once at the top it will look like the image below. Note that the logout link (top right corner) is no longer clickable.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-fKt5RGq7A3s/TtphxW0ipVI/AAAAAAAAEFg/G1P671C5oqU/s400/toolbar_top.png)](http://3.bp.blogspot.com/-fKt5RGq7A3s/TtphxW0ipVI/AAAAAAAAEFg/G1P671C5oqU/s1600/toolbar_top.png)</div>
If this happens a lot in development you could create a special button for the JavaScript code and add it as a dynamic action.
