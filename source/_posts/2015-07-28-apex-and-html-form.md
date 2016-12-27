---
title: APEX and the HTML Form
tags:
  - APEX
date: 2015-07-28 06:30:00
alias:
---

_This is the second post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page.&nbsp;__It is important to read these articles in the following order._
_
__-&nbsp;[Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)_
_-&nbsp;[APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)_
_-&nbsp;[APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)_
-&nbsp;[_Why does APEX do this? (by John Snyders)_](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)

The goal of this article is to highlight how APEX actually processes the data that is sent data from the browser to the server when a submit button is pressed.

Suppose you have a simple page with two elements: <span style="font-family: Courier New, Courier, monospace;">P1_FIRST_NAME</span> and <span style="font-family: Courier New, Courier, monospace;">P1_LAST_NAME</span> as shown below:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-WDAi-T8zHLA/VbMAclaJXOI/AAAAAAAAJzI/F6CHuKiUQEM/s320/Snip20150724_7.png)](http://2.bp.blogspot.com/-WDAi-T8zHLA/VbMAclaJXOI/AAAAAAAAJzI/F6CHuKiUQEM/s1600/Snip20150724_7.png)</div><div class="separator" style="clear: both; text-align: center;">
</div>Stripping out a lot code, the underlying HTML code for this page looks like:
<pre class="brush: html;"><form action="wwv_flow.accept" id="wwvFlowForm" method="post" name="wwv_flow">
<input name="p_arg_names" type="hidden" value="32629789701858906" />
  <input id="P1_FIRST_NAME" name="p_t01" type="text" value="" />

  <input name="p_arg_names" type="hidden" value="32629863123858907" />
  <input id="P1_LAST_NAME" name="p_t02" type="text" value="" />
</form>
</pre>You'll notice that despite their being four input elements they're really only three unique sets of names that are used:&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_arg_names, p_t01,</span> and&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_t02</span>. &nbsp;When the page is submitted the web server (APEX) will get/processes the following elements:

<span style="font-family: Courier New, Courier, monospace;">p_arg_names[1] =&nbsp;32629789701858906</span>
<span style="font-family: Courier New, Courier, monospace;">p_arg_names[2] =&nbsp;32629863123858907</span>
<span style="font-family: Courier New, Courier, monospace;">
</span><span style="font-family: Courier New, Courier, monospace;">p_t01 = Martin&nbsp;</span>
<span style="font-family: Courier New, Courier, monospace;">p_t02 = D'Souza</span>

None of the data sent back to the server make reference of <span style="font-family: Courier New, Courier, monospace;">P1_FIRST_NAME</span> or <span style="font-family: Courier New, Courier, monospace;">P2_LAST_NAME</span>. As well,&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> is stored in a top down order of how they are in the page.

<span style="font-family: Courier New, Courier, monospace;">p_arg_names</span> values are actually the IDs for each of the of the page items as highlighted in the following query:
<pre class="brush: sql;">select item_id, item_name
from apex_application_page_items
where 1=1
  and application_id = 118
  and page_id = 1;

ITEM_ID           ITEM_NAME
----------------- -------------
32629789701858906 P1_FIRST_NAME
32629863123858907 P1_LAST_NAME
</pre>APEX is able to map the data back to their corresponding APEX items by first mapping the values in&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_arg_names</span>&nbsp;to the&nbsp;<span style="font-family: Courier New, Courier, monospace;">apex_application_page_items</span>&nbsp;view and then using the values in the&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_txx</span>&nbsp;to retrieve the data that was submitted for each item.

The order that the values are submitted in for <span style="font-family: Courier New, Courier, monospace;">p_arg_names</span>&nbsp;must match the&nbsp;<span style="font-family: Courier New, Courier, monospace;">p_txx</span>&nbsp;values for APEX to correctly map the values to their appropriate APEX item. I.e. <span style="font-family: Courier New, Courier, monospace;">p_args_names[**1**]</span> will link to <span style="font-family: Courier New, Courier, monospace;">p_t0**1**</span> and <span style="font-family: Courier New, Courier, monospace;">p_arg_names[**2**]</span> will link to <span style="font-family: Courier New, Courier, monospace;">p_t0**2**</span> etc.