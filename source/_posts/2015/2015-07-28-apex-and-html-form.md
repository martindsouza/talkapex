---
title: APEX and the HTML Form
tags:
  - apex
date: 2015-07-28 06:30:00
alias:
---

_This is the second post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page. It is important to read these articles in the following order._

* [Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)
* [APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)
* [APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)
* [Why does APEX do this? (by John Snyders)](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)

The goal of this article is to highlight how APEX actually processes the data that is sent data from the browser to the server when a submit button is pressed.

Suppose you have a simple page with two elements: `P1_FIRST_NAME` and `P1_LAST_NAME` as shown below:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-WDAi-T8zHLA/VbMAclaJXOI/AAAAAAAAJzI/F6CHuKiUQEM/s320/Snip20150724_7.png)](http://2.bp.blogspot.com/-WDAi-T8zHLA/VbMAclaJXOI/AAAAAAAAJzI/F6CHuKiUQEM/s1600/Snip20150724_7.png)</div>

Stripping out a lot code, the underlying HTML code for this page looks like:
```html
<form action="wwv_flow.accept" id="wwvFlowForm" method="post" name="wwv_flow">
<input name="p_arg_names" type="hidden" value="32629789701858906" />
  <input id="P1_FIRST_NAME" name="p_t01" type="text" value="" />

  <input name="p_arg_names" type="hidden" value="32629863123858907" />
  <input id="P1_LAST_NAME" name="p_t02" type="text" value="" />
</form>
```

You'll notice that despite their being four input elements they're really only three unique sets of names that are used: `p_arg_names`, `p_t01`, and `p_t02`.  When the page is submitted the web server (APEX) will get/processes the following elements:

```
p_arg_names[1] = 32629789701858906
p_arg_names[2] = 32629863123858907

p_t01 = Martin
p_t02 = D'Souza
```

None of the data sent back to the server make reference of `P1_FIRST_NAME` or `P2_LAST_NAME`. As well, `p_arg_names` is stored in a top down order of how they are in the page.

`p_arg_names` values are actually the IDs for each of the of the page items as highlighted in the following query:

```sql
select item_id, item_name
from apex_application_page_items
where 1=1
  and application_id = 118
  and page_id = 1;

ITEM_ID           ITEM_NAME
----------------- -------------
32629789701858906 P1_FIRST_NAME
32629863123858907 P1_LAST_NAME
```

APEX is able to map the data back to their corresponding APEX items by first mapping the values in `p_arg_names` to the `apex_application_page_items` view and then using the values in the `p_txx` to retrieve the data that was submitted for each item.

The order that the values are submitted in for `p_arg_names` must match the `p_txx` values for APEX to correctly map the values to their appropriate APEX item. I.e. `p_args_names[1]` will link to `p_t01` and `p_arg_names[2]` will link to `p_t02` etc.
