---
title: APEX Static Files Not For Secure Content
tags:
  - APEX
date: 2009-06-12 09:00:00
alias:
---

Static files and Images (under Shared Components) are a great way to use external files (such as images, css, js, documents, etc). However, they should not be used to store sensitive information as users don't need to be logged in to access these files.

Here's an example: In an APEX application I've uploaded a documented called "top_secret.doc" in the Static Files section. I only want logged in users to be able to download this file. After the user logs in their is a HTML region which contains a link to top_secret.doc. The region source is:

<pre class="brush: html">
[Secure document](&APP_IMAGES.secure_doc.doc).
</pre>

When the user logs in they now see a link on the first page called "Secure document" which references top_secret.doc.

At first glance this seems secure since the user must first login before downloading the document. The hyperlink looks something like this: <span style="font-style:italic">http://localhost:8080/apex/wwv_flow_file_mgr.get_file?p_security_group_id=1037606673759910&p_flow_id=103&p_fname=top_secret.doc</span>

If you notice there's no reference to the user's APEX session ID. Anybody can use this URL to download the file even if they don't have access to your application.

<span style="color:red">This is <span style="font-weight:bold">not</span> a bug or an APEX security hole</span>, but something that you should be aware of if you are thinking about storing sensitive information in the static files area.