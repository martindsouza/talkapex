---
title: APEX and the Order Items are Submitted
tags:
  - APEX
date: 2015-07-29 06:30:00
alias:
---

_This is the last post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page.&nbsp;__It is important to read these articles in the following order._
_
__-&nbsp;[Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)_
_-&nbsp;[APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)_
_-&nbsp;[APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)_
-&nbsp;[_Why does APEX do this? (by John Snyders)_](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)

The goal of this article is to highlight the effects of moving items and regions around on a page. This article will be broken into four sections, a high level recap from the previous two articles, moving an APEX item, moving an APEX item along with its corresponding <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> element, and finally a conclusion. It will reference and build upon the example from the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html).

### **Recap**
I can't stress enough how important it is to read the previous two articles. To recap, when APEX submits the page it'll use the following values to map the HTML elements to their corresponding APEX items.

**<span style="font-family: Courier New, Courier, monospace;">APEX Item ID &nbsp; &nbsp;| &nbsp;APEX Item &nbsp; &nbsp; &nbsp;| &nbsp;Element Name<!-----><!-----><!-----></span>**
<span style="font-family: Courier New, Courier, monospace;">p_arg_names[1] &nbsp;| &nbsp;P1_FIRST_NAME &nbsp;| &nbsp;p_t01<!-----><!-----><!-----></span>
<span style="font-family: Courier New, Courier, monospace;">p_arg_names[2] &nbsp;| &nbsp;P1_LAST_NAME &nbsp; | &nbsp;p_t02</span>
<span style="font-family: Courier New, Courier, monospace;">
</span>

### Moving APEX items
<div>They're some times where it is required to move APEX items around the page after it has rendered. This example will highlight what happens when an item (and only the item) is moved.</div><div>
</div><div>Using the example from the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html)&nbsp;suppose you move <span style="font-family: Courier New, Courier, monospace;">P1_LAST_NAME</span> before <span style="font-family: Courier New, Courier, monospace;">P1_FIRST_NAME</span> using jQuery.&nbsp;</div><pre class="brush: js;">$('#P1_LAST_NAME').insertBefore('#P1_FIRST_NAME');
</pre>
The page will look like:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-6p5BIQ2B7gU/VbMQoEtJ8zI/AAAAAAAAJzY/kTU6xrhMDZE/s400/Snip20150724_9.png)](http://2.bp.blogspot.com/-6p5BIQ2B7gU/VbMQoEtJ8zI/AAAAAAAAJzY/kTU6xrhMDZE/s1600/Snip20150724_9.png)</div>
When the page is submitted, everything still works as expected. <span style="font-family: Courier New, Courier, monospace;">P1_FIRST_NAME = Martin</span> and <span style="font-family: Courier New, Courier, monospace;">P2_LAST_NAME = D'Souza</span>. In this case, it was safe to the move the item around the page since only the&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_t02</span> element was moved. The order of <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> (which APEX uses to map to <span style="font-family: Courier New, Courier, monospace;">apex_application_page_items.item_id</span> an <span style="font-family: Courier New, Courier, monospace;">p_txx</span>) was not changed.

### Moving an APEX item along with its corresponding <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> element.
Using the previous example, suppose the items were split up into two regions as shown below. To help out, each region has been assigned a static id of <span style="font-family: Courier New, Courier, monospace;">region-one</span> and <span style="font-family: Courier New, Courier, monospace;">region-two</span> respectively.

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-zjbK3J1Aztw/VbMfitUB8QI/AAAAAAAAJzo/ZMG1AhJJbN0/s320/Snip20150724_10.png)](http://1.bp.blogspot.com/-zjbK3J1Aztw/VbMfitUB8QI/AAAAAAAAJzo/ZMG1AhJJbN0/s1600/Snip20150724_10.png)</div>
Using the following code, Region Two is moved above Region One:
<pre class="brush: js;">$('#region-two').insertBefore('#region-one');
</pre>
The screen then looks like:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-VfHsSdFSn6c/VbORfzkJa0I/AAAAAAAAJz8/XjeiJy-KkV0/s320/Snip20150725_11.png)](http://2.bp.blogspot.com/-VfHsSdFSn6c/VbORfzkJa0I/AAAAAAAAJz8/XjeiJy-KkV0/s1600/Snip20150725_11.png)</div>
Referring to the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html)&nbsp;not only has the input element for <span style="font-family: Courier New, Courier, monospace;">P1_LAST_NAME</span> moved, but it's corresponding <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> hidden element was moved and its overall order has changed. Now when the page is submitted the following is sent to the server (note the order):

<span style="font-family: Courier New, Courier, monospace;">p_arg_names[1] = 32629863123858907 - Maps to APEX item P1_LAST_NAME</span>
<span style="font-family: Courier New, Courier, monospace;">p_arg_names[2] = 32629789701858906 - Maps to APEX item P1_FIRST_NAME</span>
<span style="font-family: Courier New, Courier, monospace;">
</span><span style="font-family: Courier New, Courier, monospace;">p_t01 = Martin</span>
<span style="font-family: Courier New, Courier, monospace;">p_t02 = D'Souza</span>

Just to recap and highlight what is about to happen:

<span style="font-family: Courier New, Courier, monospace;">p_arg_names[**1**] (P1_LAST_NAME) maps to p_t0**1**&nbsp;(Martin)</span>
<span style="font-family: Courier New, Courier, monospace;">p_arg_names[**2**] (P1_FIRST_NAME) maps to p_t0**2**&nbsp;(D'Souza)</span>

You can see the mis-match occur below once the page has been submitted.&nbsp;<span style="font-family: Courier New, Courier, monospace;">Martin</span> is stored in <span style="font-family: Courier New, Courier, monospace;">P1_LAST_NAME</span> and <span style="font-family: Courier New, Courier, monospace;">D'Souza</span> is stored in <span style="font-family: Courier New, Courier, monospace;">P1_FIRST_NAME</span>.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-cjcdxvzen40/VbOTYo_FyTI/AAAAAAAAJ0I/AR_ekcSGBQk/s320/Snip20150725_12.png)](http://4.bp.blogspot.com/-cjcdxvzen40/VbOTYo_FyTI/AAAAAAAAJ0I/AR_ekcSGBQk/s1600/Snip20150725_12.png)</div>

### Conclusion
Be very careful when moving APEX items around the page. As a guideline, it's usually safe to move individual items, provided you're not moving any <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> hidden elements. If you're moving a region and/or any&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_arg_names</span>&nbsp;elements you may get invalid data assignments if the order of <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> is changed.

This issue was first discussed a very long time ago between myself and [Dan McGhan](https://twitter.com/dmcghan) on the Oracle Forums: [https://community.oracle.com/message/3182532](https://community.oracle.com/message/3182532)