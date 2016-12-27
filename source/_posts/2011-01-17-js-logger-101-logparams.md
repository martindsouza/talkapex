---
title: JS Logger 1.0.1 - logParams
tags:
  - APEX
  - console wrapper
  - JavaScript
date: 2011-01-17 08:00:00
alias:
---

<span style="font-style: italic;">Note: This now has a new name, Console Wrapper, and has been moved to a Google project. Please go to [http://www.talkapex.com/2011/01/console-wrapper-previously-js-logger.html](http://www.talkapex.com/2011/01/console-wrapper-previously-js-logger.html) for more information.</span>

I've updated my [JavaScript Console Logger](http://www.talkapex.com/2010/08/javascript-console-logger.html) package to include a new function called <span style="font-style: italic;">logParams</span>. logParams will automatically log all the parameters in your function. This can save a lot of time since you don't need to manually list all the parameters and it will detect any "extra" parameters. 

Here's an example of the code: 
<pre class="brush: js;highlight:2">
function myFn(px, py, pz){
  $.console.logParams();

  //do something as part of this function

}//myFn;

//Not required if using in APEX. 
//If called from APEX it will detect if you are in debug mode
$.console.setLevel('log'); 

//Call myFn with the correct amount of parameters
myFn(1,2,3);

//Call myFn with more than "expected" number of parameters
myFn(1,2,3,'hello', 'goodbye');</pre>Which results in: 

[![](http://1.bp.blogspot.com/_33EF80fk9sM/TTN9AtDxneI/AAAAAAAAD3c/R9SZhwhs6Ec/s400/logger_1.0.1_logparams.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TTN9AtDxneI/AAAAAAAAD3c/R9SZhwhs6Ec/s1600/logger_1.0.1_logparams.jpg)<span style="font-style: italic;">Note: by default the parameters groups are collapsed by I've expanded them for this demo</span>

Download [Javascript Console Logger $logger.1.0.1](http://www.clarifit.com/files/blogs/talkapex/files/$logger_1.0.1.js). For more information about this javascript library please read my original post: [http://www.talkapex.com/2010/08/javascript-console-logger.html](http://www.talkapex.com/2010/08/javascript-console-logger.html)

Thanks to [Dan McGhan](http://www.danielmcghan.us/) for helping come up with this idea.