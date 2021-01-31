---
title: Inline Function in Dynamic Action JavaScript Expression in APEX
tags:
  - apex
  - dynamic-actions
  - javascript
date: 2021-01-31 12:37:09
alias:
---


In some Dynamic Actions (DA) settings in APEX you have the option to use a JavaScript (JS) Expression. A common example of this is setting a value where the `Set Type` is `JavaScript`:

{% asset_img js-expression.png %}

As the name (JavaScript Expression) suggests this should be an expression such as `1 + 2;` If you try to run multiple lines of code with a `return` statement (as shown above) the following error is raised:

```javascript
Uncaught TypeError: apex.da.initDaEventList is not a function
```

To resolve this issue you can use an [immediately-invoked Function Expressions (IIFE)](https://flaviocopes.com/javascript-iife/) as the JavasScript Expression. The following example re-writes the code from the first example as an IIFE and will work as a valid JavaScript Expression in the DA:

```javascript
(function() {
  var today = new Date();
  return today.getFullYear();
})()
```


Thanks to [Adrian Png](https://twitter.com/fuzziebrain) and [Trent Schafer](https://twitter.com/trentschafer) for the help on this.