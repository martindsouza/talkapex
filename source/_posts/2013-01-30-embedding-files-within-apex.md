---
title: 'Embedding Files within APEX: WORKSPACE_IMAGES vs APP_IMAGES'
tags:
  - apex
date: 2013-01-30 18:43:00
alias:
---

_This is first of a 3 part series on embedding files in your APEX application. You should read all the articles before deciding whether or not to leverage this feature in APEX as it has its pros and cons._

APEX allows you to embed static web files (CSS, JS, images, etc) into your application. This functionality removes the need to store web files on a web server which is required for some applications.

To upload your file into the application go to Shared Components &gt; Static Files. Click the Create button. On the Create page you can upload a file and either associate the file to a specific application or no application.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-eqPVNrJcaCY/UQnD2Uax-hI/AAAAAAAAETE/K9ef2OfW7Dk/s640/embed_apex_file_upload.png)](http://4.bp.blogspot.com/-eqPVNrJcaCY/UQnD2Uax-hI/AAAAAAAAETE/K9ef2OfW7Dk/s1600/embed_apex_file_upload.png)</div><div class="separator" style="clear: both; text-align: center;">
</div>Files associated to a specific application must have a unique filename within its parent application. It can then be referenced (most likely in a page template) using the #APP_IMAGES# substitution string. Ex: #APP_IMAGES#test.js

Files that are not associated with a specific application are available to all the applications within the workspace and can be referenced (most likely in a page template) using the #WORKSPACE_IMAGES# substitution string. Ex: #WORKSPACE_IMAGES#test.js

Files that are added to the application aren't stored on the web server. They are stored in the database. For high traffic applications this may not be a great idea and you may want to look at storing them on a web server.

[Part 2](http://www.talkapex.com/2013/01/embedding-files-within-apex-supporting.html) of the series will cover how to include the files in a installation script.
