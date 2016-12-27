---
title: APEX Interactive Reports - Customize Wait Display Part 1
tags:
  - apex
date: 2009-04-13 07:43:00
alias:
---

We recently needed to change, and move, the default Interactive Report (IR) AJAX "Loading" image (spinning wheel):
[![](http://3.bp.blogspot.com/_33EF80fk9sM/SeNCCZQweaI/AAAAAAAADoA/25cGNbjO0ok/s400/01_cust_ir_wait.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/SeNCCZQweaI/AAAAAAAADoA/25cGNbjO0ok/s1600-h/01_cust_ir_wait.bmp)
  After some investigation they're several things you can do depending on your requirements. A demo application is available [here.](http://apex.oracle.com/pls/otn/f?p=20195:1200)

<span style="font-weight: bold">Part 2 is here: [http://apex-smb.blogspot.com/2009/04/apex-interactive-reports-customize-wait_28.html](http://apex-smb.blogspot.com/2009/04/apex-interactive-reports-customize-wait_28.html)</span>

Before we go over the solution it's important to review where the image is located and how it gets toggled on and off. The image is stored in a span region which is hidden by default. The code looks like this:

<pre class="brush: html">
  <span style="" id="apexir_LOADER">
    ![](/i/ws/ajax-loader.gif)
  </span>
</pre>

If you want to manually make it visible you can run the following code:

<pre class="brush: js">
  $('#apexir_LOADER').show();
</pre>

The JS code to toggle the image is stored in: apex_ns_3_1.js. You can view an uncompressed version of this file in your installation directory: apex_3.1.2\apex\images\javascript\uncompressed\apex_ns_3_1.js  To toggle the image, APEX calls the following JS function: apex.worksheet.ws._BusyGraphic  (We won't be using this function in this post but I'll do another posting on how we can use it for a completely customizable solution)

First Let's build a IR page with the emp table:

<pre class="brush: sql">
select *
from emp
connect by level <= 3
</pre>

The easiest option is if all you need to do is change the image:

<pre class="brush: js">
  $('#apexir_LOADER img')[0].src="#IMAGE_PREFIX#processing3.gif";
</pre>

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SeNCdmCqZNI/AAAAAAAADoI/y3d79iimB84/s400/02_cust_ir_wait.bmp)](http://2.bp.blogspot.com/_33EF80fk9sM/SeNCdmCqZNI/AAAAAAAADoI/y3d79iimB84/s1600-h/02_cust_ir_wait.bmp)

The new image, processing3.gif, is part of APEX as well. You can reference your own images if you'd like.

In Part 2 I'll go over changing the actual function that shows and hides the loading image. This will give you complete control of the loading message.
