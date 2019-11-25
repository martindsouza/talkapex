---
title: 'Back to Basics: The HTML Form'
tags:
  - apex
date: 2015-07-27 06:30:00
alias:
---

_This is the first post in a multi-part series on how APEX submits and processes input elements from your browser to the server. The goal is to understand the effects of moving elements around the page. It is important to read these articles in the following order._

* [Back to Basics](http://www.talkapex.com/2015/07/back-to-basics-html-form.html)
* [APEX and the HTML Form](http://www.talkapex.com/2015/07/apex-and-html-form.html)
* [APEX and the Order Items are Submitted](http://www.talkapex.com/2015/07/apex-and-order-items-are-submitted.html)
* [Why does APEX do this? (by John Snyders)](http://hardlikesoftware.com/weblog/2015/07/30/apex-item-submission/)


_As the above note suggests, the next few articles will cover how APEX submits and processes input elements in a page. This article is specifically focused on the basics: HTML forms. It's important to understand how the HTML `form` tag works and posts input elements. They're other articles that cover this in much more detail and this article will only cover the high level concepts._

Any web page that submits data to it has the following structure:

```html
<form action="submitURL" method="post">
First name: <input id="foo" name="firstname" type="text" />
  Last Name: <input id="bar" name="lastname" type="text" />
  <input type="submit" />
</form>
```

This will produce a page that looks like:

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-m6WcfIpetF8/VbL4s2AWHXI/AAAAAAAAJys/-E-lg-jGPK0/s1600/Snip20150724_4.png)](http://1.bp.blogspot.com/-m6WcfIpetF8/VbL4s2AWHXI/AAAAAAAAJys/-E-lg-jGPK0/s1600/Snip20150724_4.png)</div>When the page is submitted the following is sent to the server:

```
firstname = Martin
lastname = D'Souza
```

Despite what most developers think, the element's IDs (in this case `foo` and `bar`) are not submitted to the server. The server never sees/knows about them. Instead it must use the element's `name` attribute.

HTML form behavior has another nice feature that allows for the same name to be used multiple times. Modifying the previous example, the next example will use the name `person` for both the first and last name elements:
```html
<form action="submitURL" method="post">
First name: <input id="foo" name="person" type="text" />
Last Name: <input id="bar" name="person" type="text" />
<input type="submit" />
</form>
```

Now when submitting the page the following is sent to the server:

```
person[1] = Martin
person[2] = D'Souza
```

The server processes the `person` attribute as an array of data. Using pseudo code, this is how a web server scripting language may process the data:

```
...
firstName = htmlForm.person[1];
lastName = htmlForm.person[2];
...
```

The two key items to take away from this article are:

- When elements are submitted, only the `name` attribute is sent to the web server and is used to reference the element.
- A form can contain multiple elements with the same name. In this case the web server will view them as an array of data.
