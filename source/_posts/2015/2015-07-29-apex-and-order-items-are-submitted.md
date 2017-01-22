---
title: APEX and the Order Items are Submitted
tags:
  - apex
date: 2015-07-29 06:30:00
alias:
---

_This is the last post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page. It is important to read these articles in the following order._

* [Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)
* [APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)
* [APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)
* [Why does APEX do this? (by John Snyders)](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)

The goal of this article is to highlight the effects of moving items and regions around on a page. This article will be broken into four sections, a high level recap from the previous two articles, moving an APEX item, moving an APEX item along with its corresponding `p_arg_names` element, and finally a conclusion. It will reference and build upon the example from the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html).

### Recap
I can't stress enough how important it is to read the previous two articles. To recap, when APEX submits the page it'll use the following values to map the HTML elements to their corresponding APEX items.

APEX Item ID  | APEX Item   | Element Name
--- | --- | ---
`p_arg_names[1]` | `P1_FIRST_NAME` | `p_t01`
`p_arg_names[2]` | `P1_LAST_NAME`  | `p_t02`


### Moving APEX items
They're some times where it is required to move APEX items around the page after it has rendered. This example will highlight what happens when an item (and only the item) is moved.
Using the example from the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html) suppose you move `P1_LAST_NAME` before `P1_FIRST_NAME` using jQuery. <pre class="brush: js;">$('#P1_LAST_NAME').insertBefore('#P1_FIRST_NAME');
</pre>
The page will look like:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-6p5BIQ2B7gU/VbMQoEtJ8zI/AAAAAAAAJzY/kTU6xrhMDZE/s400/Snip20150724_9.png)](http://2.bp.blogspot.com/-6p5BIQ2B7gU/VbMQoEtJ8zI/AAAAAAAAJzY/kTU6xrhMDZE/s1600/Snip20150724_9.png)</div>
When the page is submitted, everything still works as expected. `P1_FIRST_NAME = Martin` and `P2_LAST_NAME = D'Souza`. In this case, it was safe to the move the item around the page since only the `p_t02` element was moved. The order of `p_arg_names` (which APEX uses to map to `apex_application_page_items.item_id` an `p_txx`) was not changed.

### Moving an APEX item along with its corresponding `p_arg_names` element.
Using the previous example, suppose the items were split up into two regions as shown below. To help out, each region has been assigned a static id of `region-one` and `region-two` respectively.

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-zjbK3J1Aztw/VbMfitUB8QI/AAAAAAAAJzo/ZMG1AhJJbN0/s320/Snip20150724_10.png)](http://1.bp.blogspot.com/-zjbK3J1Aztw/VbMfitUB8QI/AAAAAAAAJzo/ZMG1AhJJbN0/s1600/Snip20150724_10.png)</div>
Using the following code, Region Two is moved above Region One:

```js
$('#region-two').insertBefore('#region-one');
```

The screen then looks like:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-VfHsSdFSn6c/VbORfzkJa0I/AAAAAAAAJz8/XjeiJy-KkV0/s320/Snip20150725_11.png)](http://2.bp.blogspot.com/-VfHsSdFSn6c/VbORfzkJa0I/AAAAAAAAJz8/XjeiJy-KkV0/s1600/Snip20150725_11.png)</div>

Referring to the [previous article](http://www.talkapex.com/2015/07/apex-and-html-form.html) not only has the input element for `P1_LAST_NAME` moved, but it's corresponding `p_arg_names` hidden element was moved and its overall order has changed. Now when the page is submitted the following is sent to the server (note the order):

```
p_arg_names[1] = 32629863123858907 - Maps to APEX item P1_LAST_NAME
p_arg_names[2] = 32629789701858906 - Maps to APEX item P1_FIRST_NAME

p_t01 = Martin
p_t02 = D'Souza
```

Just to recap and highlight what is about to happen:

`p_arg_names[1] (P1_LAST_NAME)` maps to `p_t01 (Martin)`
`p_arg_names[2] (P1_FIRST_NAME)` maps to `p_t02 (D'Souza)`

You can see the mis-match occur below once the page has been submitted. `Martin` is stored in `P1_LAST_NAME` and `D'Souza` is stored in `P1_FIRST_NAME`.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-cjcdxvzen40/VbOTYo_FyTI/AAAAAAAAJ0I/AR_ekcSGBQk/s320/Snip20150725_12.png)](http://4.bp.blogspot.com/-cjcdxvzen40/VbOTYo_FyTI/AAAAAAAAJ0I/AR_ekcSGBQk/s1600/Snip20150725_12.png)</div>

### Conclusion
Be very careful when moving APEX items around the page. As a guideline, it's usually safe to move individual items, provided you're not moving any `p_arg_names` hidden elements. If you're moving a region and/or any `p_arg_names` elements you may get invalid data assignments if the order of `p_arg_names` is changed.

This issue was first discussed a very long time ago between myself and [Dan McGhan](https://twitter.com/dmcghan) on the Oracle Forums: [https://community.oracle.com/message/3182532](https://community.oracle.com/message/3182532)
