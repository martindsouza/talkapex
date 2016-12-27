---
title: How to Avoid Bot Spammers in APEX
tags:
  - APEX
date: 2010-02-11 09:00:00
alias:
---

If you've ever developed a public web application with a form on it you may notice that you may get bot spammers trying to enter information into your application.

There's a simple trick that a friend of mine, Sean Rabey, at [Pump Interactive](http://www.pumpinteractive.ca/) showed me which will help you reject submissions from bots. 

Sean suggested that I use an input field and then hide it with CSS. Humans entering data into the form won't see the field and therefore won't enter anything into it. Bots on the other hand may try to fill out this field and can't detect whether or not it's visible in the browser. If your "special" field has data in it you can reject the submission since you know it's not a human entering the data.

Here's an example of how you can do this in APEX. You can view an example here: [http://apex.oracle.com/pls/apex/f?p=20195:2900](http://apex.oracle.com/pls/apex/f?p=20195:2900)

<span style="font-weight:bold;">- Create a "Dummy" item </span>
Set "HTML Form Element Attributes" to class="hideMe"

[![](http://1.bp.blogspot.com/_33EF80fk9sM/S3OkFBpPIZI/AAAAAAAADwQ/ltVMMS3JCD0/s320/step1.png)](http://1.bp.blogspot.com/_33EF80fk9sM/S3OkFBpPIZI/AAAAAAAADwQ/ltVMMS3JCD0/s1600-h/step1.png)

<span style="font-weight:bold;">- Configure "hideMe" style</span>
Add the following in your application somewhere (or to a CSS file)
<pre class="brush: html">
<style>
  .hideMe {display:none}
</style>
</pre>

<span style="font-weight:bold;">- Add validation to catch bot entries</span>
Type: Exists
Validation Expression 1:
<pre class="brush: sql">
SELECT 1
  FROM DUAL
 WHERE :p2900_dummy IS NULL
</pre>