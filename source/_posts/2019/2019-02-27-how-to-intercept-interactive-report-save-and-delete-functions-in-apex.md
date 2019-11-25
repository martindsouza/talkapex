---
title: How to Intercept Interactive Report Save and Delete Functions in APEX
tags:
  - apex
  - javascript
date: 2019-02-27 20:04:19
alias:
---


Suppose you have a requirement that all saved Interactive Reports (IR) names must be lowercase. How can you enforce it? Using some JavaScript (JS) you can "intercept" the save and delete functions and either continue on with them or not. _Note my uses case was very different than a naming standard but using it as an example as it's very simple._


The following code snippet will intercept the save functionality (you can inject your business rules accordingly). It's important to note that this code **must be run** in the page's `Function and Global Variable Declaration` section (as part of the page's properties). A better alternative is to load it via a JS file (_hint: use [APEX Nitro](https://github.com/OraOpenSource/apex-nitro)_). It will not work if you try to run it as is in your browser's console.

<span class="talkapex-warning">_Warning: The following code is using undocumented features in the APEX IR JS library. Use at your discretion._</span>

```javascript
$(document).ready(function() {

  $.widget("apex.interactiveReport", $.apex.interactiveReport, {
    
    // Saving a Default report
    _saveDefault: function () {
      console.log("_saveDefault");
      console.log(this);
      console.log(this._getId("report_name"));
      console.log('stopping');
      // return this._super();
    },

    // Saving a Public or Private reports
    _save: function () {
      console.log('_save');
      console.log(this);
      console.log(this._getId("report_name"));
      console.log('stopping');
      // return this._super();
    },

    // Deleting a report (user clicking the "X")
    _remove: function () {
      console.log('_remove');
      console.log(this);
      console.log(this._getId("report_name"));
      console.log('stopping');
      // return this._super();
    }
  });

});
```

I've commented out `return this._super();`. Re-enabling it will continue the function (save, remove, etc) as expected. In most cases you would wrap it in an `if` condition based on your requirements.

The following demo shows this code in action. Both the save and delete requests don't complete as the JS code intercepts the requests and logs some information.

{% asset_img intercept-ir.gif %}

