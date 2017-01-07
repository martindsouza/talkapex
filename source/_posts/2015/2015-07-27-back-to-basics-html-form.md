---
title: 'Back to Basics: The HTML Form'
tags:
  - apex
date: 2015-07-27 06:30:00
alias:
---

_This is the first post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page.&nbsp;__It is important to read these articles in the following order._
_
__- [Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)_
_-&nbsp;[APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)_
_-&nbsp;[APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)_
-&nbsp;[_Why does APEX do this? (by John Snyders)_](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)
_
_As the above note suggests, the next few articles will cover how APEX submits and processes input elements in a page. This article is specifically focused on the basics: HTML forms. It's important to understand how the HTML <span style="font-family: Courier New, Courier, monospace;">form</span> tag works and posts input elements. They're other articles that cover this in much more detail and this article will only cover the high level concepts.

Any web page that submits data to it has the following structure:
<pre class="brush: html;"><form action="submitURL" method="post">
First name: <input id="foo" name="firstname" type="text" />
  Last Name: <input id="bar" name="lastname" type="text" />
  <input type="submit" />
</form>
</pre>This will produce a page that looks like:

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-m6WcfIpetF8/VbL4s2AWHXI/AAAAAAAAJys/-E-lg-jGPK0/s1600/Snip20150724_4.png)](http://1.bp.blogspot.com/-m6WcfIpetF8/VbL4s2AWHXI/AAAAAAAAJys/-E-lg-jGPK0/s1600/Snip20150724_4.png)</div>When the page is submitted the following is sent to the server:

<span style="font-family: Courier New, Courier, monospace;">firstname = Martin</span>
<span style="font-family: Courier New, Courier, monospace;">lastname = D'Souza</span>

Despite what most developers think, the element's IDs (in this case <span style="font-family: Courier New, Courier, monospace;">foo</span> and <span style="font-family: Courier New, Courier, monospace;">bar</span>) are not submitted to the server. The server never sees/knows about them. Instead it must use the element's <span style="font-family: Courier New, Courier, monospace;">name</span> attribute.

HTML form behaviour has another nice feature that allows for the same name to be used multiple times. Modifying the previous example, the next example will use the name <span style="font-family: Courier New, Courier, monospace;">person</span> for both the first and last name elements:
<pre class="brush: html;"><form action="submitURL" method="post">
First name: <input id="foo" name="person" type="text" />
Last Name: <input id="bar" name="person" type="text" />
<input type="submit" />
</form>
</pre>Now when submitting the page the following is sent to the server:

<span style="font-family: Courier New, Courier, monospace;">person[1] = Martin</span>
<span style="font-family: Courier New, Courier, monospace;">person[2] = D'Souza</span>

The server processes the <span style="font-family: Courier New, Courier, monospace;">person</span> attribute as an array of data. Using pseudo code, this is how a web server scripting language may process the data:

<span style="font-family: Courier New, Courier, monospace;">...</span>
<span style="font-family: Courier New, Courier, monospace;">firstName = htmlForm.person[1];</span>
<span style="font-family: Courier New, Courier, monospace;">lastName = htmlForm.person[2];</span>
<span style="font-family: Courier New, Courier, monospace;">...</span>

The two key items to take away from this article are:

- When elements are submitted, only the <span style="font-family: Courier New, Courier, monospace;">name</span> attribute is sent to the web server and is used to reference the element.
- A form can contain multiple elements with the same name. In this case the web server will view them as an array of data.
