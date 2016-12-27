---
title: 'JavaScript: ? = If Else'
tags:
  - APEX
  - JavaScript
date: 2009-07-20 09:00:00
alias:
---

<span style="font-style:italic">This is not directly related to APEX however it will help if you use a lot of JavaScript</span>

When I first started to develop web based applications and used JavaScript I came across some JS code with a <span style="font-weight:bold;">?</span> in it. I didn't know what it was for and Googling "JavaScript ?" didn't help either. Here's a quick summary on how it works

<span style="font-weight:bold;">Boolean ? true action : false action</span>

So this:
<pre class="brush: js">
var x = 2;
if (x > 1)
  window.alert('True');
else
  window.alert('False');
</pre>  

Becomes this: 
<pre class="brush: js">
var x = 2;
x>1 ? window.alert('True') : window.alert('False') ;
</pre>