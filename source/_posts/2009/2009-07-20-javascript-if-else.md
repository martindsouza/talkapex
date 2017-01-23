---
title: 'JavaScript: ? = If Else'
tags:
  - javascript
date: 2009-07-20 09:00:00
alias:
---

_This is not directly related to APEX however it will help if you use a lot of JavaScript_

When I first started to develop web based applications and used JavaScript I came across some JS code with a `?` in it. I didn't know what it was for and Googling `JavaScript ?` didn't help either. Here's a quick summary on how it works

`Boolean ? true action : false action`

So this:
```js
var x = 2;
if (x > 1)
  window.alert('True');
else
  window.alert('False');
```

Becomes this:
```js
var x = 2;
x>1 ? window.alert('True') : window.alert('False') ;
```
