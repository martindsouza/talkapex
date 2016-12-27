---
title: 'APEX Plugin: Javascript Attributes'
tags:
  - apex-plugin
  - apex
  - javascript
date: 2010-08-17 21:04:00
alias:
---

Recently I was developing an APEX dynamic action plugin and ran into a bit of an issue. One of the custom attributes that I defined was a yes/no attribute which returned "true" or "false" respectively.

When I was testing my plugin it always behaved as though the user had selected <span style="font-style:italic;">yes/true</span> even though they selected <span style="font-style:italic;">no/false</span>. After some debugging I saw that the plugin values in Javascript were strings. Clearly strings are not booleans and they behave differently when evaluated.

To resolve this problem I just changed my if statement:
<pre class="brush: js;">
//From
if (this.action.attribute01)
  //do something

//To
if (this.action.attribute01.toLowerCase() == 'true')
  //do something
</pre>  
If you're new to JavaScript, here's how JavaScript handles strings when doing comparisons:
<pre class="brush: js;">
x = null;
console.log(x, x ? true : false);
x = '';
console.log(x, x ? true : false);
x = 'false';
console.log(x, x ? true : false);
x = 'true';
console.log(x, x ? true : false);
</pre>
Here are the results:

[![](http://4.bp.blogspot.com/_33EF80fk9sM/TGtLkCDK8CI/AAAAAAAADyo/XOrvWLV0pBw/s400/plugin_boolean.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/TGtLkCDK8CI/AAAAAAAADyo/XOrvWLV0pBw/s1600/plugin_boolean.jpg)
As you can see a string returns <span style="font-style:italic;">true</span> if it contains some data.
