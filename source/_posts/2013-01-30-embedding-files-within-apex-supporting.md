---
title: 'Embedding Files within APEX: Supporting Objects'
tags:
  - apex
date: 2013-01-30 18:46:00
alias:
---

_This is part two of a three part series on embedding files in your APEX application. You should read all the articles ([Part 1](http://www.talkapex.com/2013/01/embedding-files-within-apex.html)__<i>)_ before deciding whether or not to leverage this feature in APEX as it has its pros and cons.</i>

A major misconception when uploading a file into APEX is that it will be included when you export the application. Though I agree that makes the most sense, unfortunately it doesn't work this way. You need to explicitly include an installation script that will copy the file into the workspace where the application is being imported to. Sound confusing? Unfortunately it is.

In the previous post I uploaded a file into an application. If I were to export then import this application into another workspace that file wouldn't be included. To explicitly _include_ the file you need to create a Supporting Object installation script:

- Go to Application &gt; Supporting Objects &gt; Installation Scripts and click the Create button.
- At the bottom, under the Tasks heading select "Create Scripts to Install Files".

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-GZ1zNitve_c/UQnGhRWKpVI/AAAAAAAAETU/53qhONy7SVM/s640/embed_apex_file_install_script_01.png)](http://2.bp.blogspot.com/-GZ1zNitve_c/UQnGhRWKpVI/AAAAAAAAETU/53qhONy7SVM/s1600/embed_apex_file_install_script_01.png)</div>
&nbsp;- Select the file that is required for the application and click the Create Script button.

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/--4987MLwu98/UQnJgPVEB6I/AAAAAAAAETc/Obl0w5HWcZs/s640/embed_apex_file_install_script_02.jpg)](http://2.bp.blogspot.com/--4987MLwu98/UQnJgPVEB6I/AAAAAAAAETc/Obl0w5HWcZs/s1600/embed_apex_file_install_script_02.jpg)</div>
At this point in time, APEX will encode (base64) the file (in <u>**it's current state**</u>) and create a PL/SQL installation script that can be run when you import the application. <u>**If you change the file (_in Shared Components &gt; Static Files_) ****it has no impact on the installation script**</u> (i.e. it's a snapshot of the file at this point in time).

Not updating the installation script with the latest version of the file is where a lot of issues occur** **when migrating the application from different environments. After someone initially creates the installation script, there's a high probability that you may change the base file, forgetting to update the installation script. This is one of the main reasons why I don't usually recommend embedding files into my application.

[Part 3](http://www.talkapex.com/2013/01/embedding-files-within-apex-where.html) of the series will cover some issues with embedding files in an application.
