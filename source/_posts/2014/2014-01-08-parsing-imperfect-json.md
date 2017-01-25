---
title: Parsing Imperfect JSON
tags:
  - javascript
date: 2014-01-08 07:00:00
alias:
---

In JavaScript, each JSON attribute name should be wrapped in quotes for it to be parsed correctly. Sometimes you may receive a JSON string that isn't properly formed because of the missing quotes. You can still parse it using the technique described below.

To start, here's an example of malformed JSON using [`JSON.parse`](http://json.parse/) and jQuery's [`$.parseJSON`](http://api.jquery.com/jQuery.parseJSON/). You'll notice that it doesn't parse the JSON string since it doesn't have its names wrapped with quotes.

```js
//Using native JS: JSON.parse
//Similar result for jQuery $.parseJSON
var jsonStr = '{ename : "martin", sal: 100}';
var jsonObj = JSON.parse(jsonStr);

//Raises error:
SyntaxError: JSON.parse: expected property name or '}'

//Correct JSON object (notice attribute names are quoted)
var jsonStr = '{"ename" : "martin", "sal": 100}';
var jsonObj = JSON.parse(jsonStr);
console.log (jsonObj);

//Returns JSON object
Object { ename="martin", sal=100}
```

To get around this issue you can use JavaSript's [`eval`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval) method. It's not pretty but it does the job and will probably save you a lot of time trying to correct your JSON object.

```js
var jsonStr = '{ename : "martin", sal: 100}';
eval('jsonObj = ' + jsonStr + ';');
console.log(jsonObj);

//Returns JSON object
Object { ename="martin", sal=100}
```
I've found this technique really helpful when passing back JSON strings from APEX AJAX calls (via a Dynamic Action, Plugin, or custom AJAX).

**Update**: Please read John's comment below as he makes a good point that the data should be data that you control otherwise perfect area for a JS injection attack.
