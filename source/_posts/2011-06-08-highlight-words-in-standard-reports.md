---
title: Highlight Words in Standard Reports
tags:
  - APEX
  - ORACLE APEX
date: 2011-06-08 06:22:00
alias:
---

After creating a plugin I received a lot of questions about how to enable column highlighting as shown in the following screen shot:

[![](http://1.bp.blogspot.com/_33EF80fk9sM/TRGYR8daaVI/AAAAAAAAD2U/FmqujzAQRT4/s400/instant_search_demo.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TRGYR8daaVI/AAAAAAAAD2U/FmqujzAQRT4/s1600/instant_search_demo.jpg)Column highlighting is not part of the plug-in. It is a built-in feature in APEX that has been around for a very long time. They're two ways to enable column highlighting in standard reports. The first is through the create report wizard. When you enable searching, as shown below, the columns that are selected to be searchable will automatically have column highlighting enabled on then.

[![](http://4.bp.blogspot.com/-wuBQkBkYWiA/Te9mwLZuMpI/AAAAAAAAD7c/0w5RtMX_kak/s400/enable_search.jpg)](http://4.bp.blogspot.com/-wuBQkBkYWiA/Te9mwLZuMpI/AAAAAAAAD7c/0w5RtMX_kak/s1600/enable_search.jpg)To manually highlight a column edit the column. Scroll down to the Column Formatting region shown below. In the Highlight Words field you can enter a comma delimited list of words to search for or reference APEX items using the <span style="font-style: italic;">&amp;ITEM_NAME.</span> syntax. <span style="font-style: italic;">Note: for more on APEX variable syntax read [this post](http://www.talkapex.com/2011/01/variables-in-apex.html).</span>

[![](http://3.bp.blogspot.com/-vKSKi6Az-jM/Te9l8JT1W8I/AAAAAAAAD7U/lngFL8hD218/s400/highlight_words.jpg)](http://3.bp.blogspot.com/-vKSKi6Az-jM/Te9l8JT1W8I/AAAAAAAAD7U/lngFL8hD218/s1600/highlight_words.jpg)