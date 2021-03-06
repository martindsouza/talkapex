---
title: Console Wrapper 1.0.4 - Beta
tags:
  - javascript
date: 2013-03-27 23:29:00
alias:
---

Recently [Dimitri Gielis](http://dgielis.blogspot.ca/) sent me an email letting me know that there was a bug with [Console Wrapper](https://code.google.com/p/js-console-wrapper/) 1.0.3. He also include the following screen shot with the error:

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-2pPWyBLOzAQ/UVPQjtlsMRI/AAAAAAAAEUg/M9kmhH7LGfw/s640/console_wrapper_ie9_error.png)](http://2.bp.blogspot.com/-2pPWyBLOzAQ/UVPQjtlsMRI/AAAAAAAAEUg/M9kmhH7LGfw/s1600/console_wrapper_ie9_error.png)</div>
In case you can't see it, the error is `Object doesn't support property or method 'apply'`. It turns out that the issue was caused by a change in IE9 and IE10 on how they handle the `console` object. The following article describes the issue in detail: [http://whattheheadsaid.com/2011/04/internet-explorer-9s-problematic-console-object](http://whattheheadsaid.com/2011/04/internet-explorer-9s-problematic-console-object)

<strike>I've fixed Console Wrapper to handle the issues presented in IE9 and IE10. Before I officially release 1.0.4 on [https://code.google.com/p/js-console-wrapper/](https://code.google.com/p/js-console-wrapper/) I'd like some people to beta test it. If you're interested in testing 1.0.4 you can download it [here](https://www.dropbox.com/s/g5zzz4qexj8p9jc/%24console_wrapper_1.0.4.js). If you find any issues please send me an email (my email address is in the comments section at the top).</strike>

_Update: 6-Apr-2013: 1.0.4 has been officially released and is now available to download on the official [project page](https://code.google.com/p/js-console-wrapper/)._
