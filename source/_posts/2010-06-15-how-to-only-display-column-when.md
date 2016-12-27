---
title: How to only Display Column when Downloading
tags:
  - APEX
  - ORACLE APEX
date: 2010-06-15 09:00:00
alias:
---

Sometimes you may have a column that contains inline HTML. A simple example of this is if you have a column called "color" and you want to display the color in the report. Your query would look like this:
<pre class="brush: sql;">
SELECT '<span style="color:' || color || '">"' || color || '</span>' color
FROM my_colors
</pre><span style="font-style:italic;">Note: That they're ways to get around this simple example using Report Templates and Column Formatting. I'm just using it for demo purposes</span>

If you were to download this report the "color" column would contain all the html (i.e. span tags etc). This may confuse users since they expected to see "red, green, blue, etc..." in their download file, but instead see the colors wrapped in a lot of html.

A workaround that I've used is to create 2 columns: color_html and color
<pre class="brush: sql;">
SELECT '<span style="color:' || color || '">"' || color || '</span>' color_html, 
            color color
  FROM my_colors
</pre>And modify each report column's attributes:

<span style="font-style:italic; font-weight:bold">Standard Reports</span>

<span style="font-style:italic;">color_html</span>
Column Definition:
Include In Export: No

<span style="font-style:italic;">color</span>
Conditional Display:
PL/SQL Function Body Returning a Boolean: return apex_application.g_excel_format

<span style="font-style:italic; font-weight:bold">Interactive Reports</span>

<span style="font-style:italic;">color_html</span>
Conditional Display:
Request is NOT Contained within Expression 1: CSV,HTMLD

<span style="font-style:italic;">color</span>
Conditional Display:
Request is Contained within Expression 1: CSV,HTMLD

Now when a user downloads the report they'll get the non-html version of the column. This solution works in APEX 4.0 and APEX 3.x