---
title: How to access images in APEX on Linux
tags:
  - APEX
date: 2008-12-28 10:49:00
alias:
---

Hi,

Dietmar has a great post on where the images are located in APEX using webdav: http://daust.blogspot.com/2006/03/where-are-images-of-application.html  He also covers how to access them via FTP!

I recently switched from Windows to Linux and was looking for a quick way to access the files as I would with Windows.

Using KDE and Konqueror, you can access them very easily. Use the following in the URL: webdav://&lt;url&gt;

So it would be: webdav://127.0.0.1:8080/i

Log in with system and you should be able to access everything!

Martin