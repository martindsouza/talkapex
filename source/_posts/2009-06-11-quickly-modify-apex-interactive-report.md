---
title: Quickly Modify APEX Interactive Report Options
tags:
  - APEX
date: 2009-06-11 09:00:00
alias:
---

If your APEX application has many Interactive Reports (IR) it can be tedious to configure IR features for each report, and their columns, such as filtering, highlighting etc.

Since APEX resides within the database there's a quick way to manage all of your IRs. <span style="color:red; font-style=italic;">Please note this is not supported by Oracle so please be aware of this.</span>

First login to the database as SYS or SYSTEM.

Update Interactive Report options: <span style="font-style: italic;">You can modify more options by looking at the table definition for wwv_flow_worksheets</span>

[![](http://1.bp.blogspot.com/_33EF80fk9sM/Si3Q3CeFohI/AAAAAAAADpY/sPTJxC7mV6Q/s400/24_ir_options.bmp)](http://1.bp.blogspot.com/_33EF80fk9sM/Si3Q3CeFohI/AAAAAAAADpY/sPTJxC7mV6Q/s1600-h/24_ir_options.bmp)

<pre class="brush: sql">
UPDATE apex_030200.wwv_flow_worksheets -- Where apex_030200 is your current APEX instance
   SET allow_report_saving = 'Y', -- Configure options as required
       show_finder_drop_down = 'N',
       show_display_row_count = 'Y',
       show_search_bar = 'N',
       show_search_textbox = 'Y',
       show_actions_menu = 'Y',
       show_select_columns = 'N',
       show_sort = 'N',
       show_filter = 'Y',
       show_control_break = 'Y',
       show_highlight = 'Y',
       show_computation = 'N',
       show_aggregate = 'N',
       show_chart = 'Y',
       show_flashback = 'N',
       show_reset = 'Y',
       show_download = 'Y',
       show_help = 'N'
 WHERE flow_id = :app_id
   AND page_id = :app_page_id -- Remove this predicate to push changes for all IRs
</pre>

Update Interactive Report Columns:

[![](http://2.bp.blogspot.com/_33EF80fk9sM/Si3Q8jhgbPI/AAAAAAAADpg/BuXPQH8UR80/s400/24_ir_col_options.bmp)](http://2.bp.blogspot.com/_33EF80fk9sM/Si3Q8jhgbPI/AAAAAAAADpg/BuXPQH8UR80/s1600-h/24_ir_col_options.bmp)

<pre class="brush: sql">
UPDATE apex_030200.wwv_flow_worksheet_columns
   SET allow_sorting = 'Y',
       allow_filtering = 'N',
       allow_ctrl_breaks = 'Y',
       allow_aggregations = 'N',
       allow_computations = 'Y',
       allow_charting = 'Y'
 WHERE flow_id = :app_id
   AND page_id = :app_page_id;
</pre>