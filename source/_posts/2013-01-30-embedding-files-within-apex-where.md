---
title: 'Embedding Files within APEX: Where things go wrong'
tags: []
date: 2013-01-30 18:50:00
alias:
---

_This is the final part of a three part series on embedding files in your  APEX application. You should read all the articles ([Part 1](http://www.talkapex.com/2013/01/embedding-files-within-apex.html) and [Part 2](http://www.talkapex.com/2013/01/embedding-files-within-apex-supporting.html)) before deciding  whether or not to leverage this feature in APEX as it has its pros and  cons._

The previous posts in this series covered how to upload, reference, and create installation scripts for embedded files within APEX. This post will cover some caveats that people may not be aware of when using embedded files within APEX.

I already hinted in the first post of this series that they're several reasons why you may not want to embed files within APEX. Besides performance issues they're situations which you'll get some unexpected behavior. I've included examples of some of these issues below. Each example assumes that you have a static file (test.js) included in the base application and it's also in the installation scripts.

#### **Copying an Application**&nbsp;
A lot of times people tend to copy an application within a workspace to back it up before doing a big change or to run some one-off tests on their own. If you copy an application, despite selecting Yes for "Copy Supporting Objects Definitions" the application specific files are not copied.

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-50h21B0FQ7A/UQnKA5tLbGI/AAAAAAAAETk/RkK0e80NlRA/s640/embed_apex_file_copy_app.jpg)](http://1.bp.blogspot.com/-50h21B0FQ7A/UQnKA5tLbGI/AAAAAAAAETk/RkK0e80NlRA/s1600/embed_apex_file_copy_app.jpg)</div>
In this case you'll need to manually run the Supporting Objects &gt; Install Supporting Objects to install the file (assuming that the installation script has the latest version of the file). But wait. When you do that it'll actually remove the file from the original application and install it in the new one (i.e. it no longer exists in the application that you copied from).

#### Deleting a Copied Application
If you copied an application and selected "Yes" for "Copy Supporting Object Definitions" we know from the previous example that it doesn't install any embedded files. If you now delete the newly copied application and check off "Deinstall Supporting Objects" it will also remove any files from the base application that you copied from.

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-NiIJ19RZAr8/UQnK8aJ14tI/AAAAAAAAETs/1DLfMOLBjSI/s400/embed_apex_file_deinstall_app.png)&nbsp;](http://2.bp.blogspot.com/-NiIJ19RZAr8/UQnK8aJ14tI/AAAAAAAAETs/1DLfMOLBjSI/s1600/embed_apex_file_deinstall_app.png)</div><div class="separator" style="clear: both; text-align: center;">
</div>

### Summary
If embedding files in your APEX application sounds confusing don't worry, it is. I had to do many test cases while writing this blog to confirm some of the scenarios.

If you've read through the entire three part series on embedding files in your APEX application you'll notice that it can be very confusing and cumbersome_. _This can cause a lot of unexpected issues in the long run.

As much as possible I avoid embedding files in APEX and instead store them on a web server. To me, it's just not worth the hassle. Of course they're exceptions. If I can't get access to the web server or if the application is to be bundled as a stand alone application then it makes sense._ _