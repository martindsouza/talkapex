---
title: JavaScript Console Logger
tags:
  - APEX
  - console wrapper
  - JavaScript
  - ORACLE APEX
date: 2010-08-30 08:00:00
alias:
---

<span style="font-style: italic;">Note: This now has a new name, Console Wrapper, and has been moved to a Google project. Please go to [http://www.talkapex.com/2011/01/console-wrapper-previously-js-logger.html](http://www.talkapex.com/2011/01/console-wrapper-previously-js-logger.html) for more information.</span>

If you've been developing APEX applications for while, or any web applications for that matter, you'll eventually leverage the browser <span style="font-style: italic;">console</span>. If you've used Firebug, then you've had the console accessible to you.

In case you're not sure what console is, it allows you to display debug information in a nice console window within your browsers. Most (i.e. all browsers but IE) are console enabled. The most common use of console is the <span style="font-style: italic;">console.log</span> command:
<pre class="brush: js">
console.log('hello world');</pre>
Console has a lot of great features. One drawback with it is that you have to remove calls to console before putting your code into production since not all browsers support console.

Removing instrumentation code before going into production can be annoying, especially if you need to debug it later on. To resolve this issue I've created a console wrapper. This allows you to leave your debugging calls in production code. Here are some features:

*   Works in all browsers. If run in IE nothing will happen, but no error message.
*   Allows you to set Levels. By default no messages will appear.
*   Support for jQuery chaining (see examples).
*   Will automatically set level to "log" if run in APEX and APEX is in Debug mode.
A copy of the console wrapper is available here: [Download $logger_1.0.0](http://www.clarifit.com/files/blogs/talkapex/files/$logger_1.0.0.zip).

The only change that you'll need to make is call <span style="font-style: italic;">$.console</span> instead of <span style="font-style: italic;">console</span>. The download file includes a HTML file with demos. I'd recommend looking at the .js file as well for inline documentation. 

Here's an example along with its output:
<pre class="brush: js">
$.console.setLevel('log');
$.console.log('Current Level', $.console.getLevel());
$.console.group('Available Levels');
$.each($.console.levels, function(i, val){
   $.console.log(i);
});
$.console.groupEnd();
$.console.log(($.console.isApexDebug()) ? 'In APEX debug mode' : 'Not in APEX debug mode');
$.console.group('Demo Chaining');
//Notice how you can write this out all in 1 line!
$('.red').log('Before Red').css({color:'red'}).log('After Red');
$.console.groupEnd();
$.console.info('Turning off console');
$.console.setLevel('off');</pre>
[![](http://1.bp.blogspot.com/_33EF80fk9sM/THsX53hIU6I/AAAAAAAADzA/TWxAfdmQ_ng/s400/logger_demo.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/THsX53hIU6I/AAAAAAAADzA/TWxAfdmQ_ng/s1600/logger_demo.jpg)
For more information about console please read the following articles:

- Console APIs: [http://getfirebug.com/wiki/index.php/Console_API](http://getfirebug.com/wiki/index.php/Console_API)
- Firebug console example: [http://getfirebug.com/logging](http://getfirebug.com/logging)
- Console tutorial: [http://www.tuttoaster.com/learning-javascript-and-dom-with-console/](http://www.tuttoaster.com/learning-javascript-and-dom-with-console/)