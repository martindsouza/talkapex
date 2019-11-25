---
title: 'APEX IR: Column Help'
tags:
  - apex
date: 2010-09-09 08:00:00
alias:
---

I was working with an Interactive Report (IR) in APEX 4.0 and noticed that when the column header is clicked there's a Column Information button:

[![](http://2.bp.blogspot.com/_33EF80fk9sM/TIg-HV9qUOI/AAAAAAAADzI/qyKKQa8j6Hc/s400/ir_column_info_icon.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/TIg-HV9qUOI/AAAAAAAADzI/qyKKQa8j6Hc/s1600/ir_column_info_icon.jpg)
When clicked it displays the column help:

[![](http://1.bp.blogspot.com/_33EF80fk9sM/TIg-Hp9ooNI/AAAAAAAADzQ/Hi96X7SAiZ4/s400/ir_column_info_text.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TIg-Hp9ooNI/AAAAAAAADzQ/Hi96X7SAiZ4/s1600/ir_column_info_text.jpg)
At first I thought this was a new APEX 4.0 feature and then I tested it on a 3.2 instance and noticed that the column information button was present as well.

Either way, it's a really nice feature to have since you can provide column information to users.

The following query helps to identify which IR columns have the default "No help available for this page item." message. <span style="font-style:italic;">You may want to change this since it says "... page item." rather than "column"</span>
<pre class="brush: sql">
SELECT ap.page_id,
       ap.page_name,
       irc.column_alias,
       irc.help_text
  FROM apex_application_page_ir_col irc, apex_application_pages ap
 WHERE irc.application_id = :app_id
   AND irc.application_id = ap.application_id
   AND irc.page_id = ap.page_id
   AND irc.help_text = 'No help available for this page item.' -- Default help text message
</pre>
