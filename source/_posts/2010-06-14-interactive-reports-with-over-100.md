---
title: Interactive Reports with over 100 Columns
tags:
  - APEX
  - ORACLE APEX
date: 2010-06-14 09:00:00
alias:
---

I ran into an issue last week where I had an Interactive Report (IR) with over 100 columns. All 100 plus columns displayed for end users, however I was only able to modify the first 100 columns. For example, the 101st column did not display in the report attributes screen (see screen shot below)

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TBWayGifFFI/AAAAAAAADww/PSLxmo75XJM/s400/ir_too_many_cols.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TBWayGifFFI/AAAAAAAADww/PSLxmo75XJM/s1600/ir_too_many_cols.jpg)

After some digging around I figured out how to modify columns that weren't displayed on the Report Attributes screen. This demo requires Firefox and Firebug. You should also be able to do this in Google Chrome. This demo works for APEX 3.x and APEX 4.0.
<span style="font-weight:bold; font-style:italic">
Build Large IR:</span>
<pre class="brush: sql;">
SELECT LEVEL c001, LEVEL c002, LEVEL c003, LEVEL c004, LEVEL c005, LEVEL c006, LEVEL c007,
       LEVEL c008, LEVEL c009, LEVEL c010, LEVEL c011, LEVEL c012, LEVEL c013, LEVEL c014, 
       LEVEL c015, LEVEL c016, LEVEL c017, LEVEL c018, LEVEL c019, LEVEL c020, LEVEL c021, 
       LEVEL c022, LEVEL c023, LEVEL c024, LEVEL c025, LEVEL c026, LEVEL c027, LEVEL c028, 
       LEVEL c029, LEVEL c030, LEVEL c031, LEVEL c032, LEVEL c033, LEVEL c034, LEVEL c035, 
       LEVEL c036, LEVEL c037, LEVEL c038, LEVEL c039, LEVEL c040, LEVEL c041, LEVEL c042, 
       LEVEL c043, LEVEL c044, LEVEL c045, LEVEL c046, LEVEL c047, LEVEL c048, LEVEL c049, 
       LEVEL c050, LEVEL c051, LEVEL c052, LEVEL c053, LEVEL c054, LEVEL c055, LEVEL c056, 
       LEVEL c057, LEVEL c058, LEVEL c059, LEVEL c060, LEVEL c061, LEVEL c062, LEVEL c063,
       LEVEL c064, LEVEL c065, LEVEL c066, LEVEL c067, LEVEL c068, LEVEL c069, LEVEL c070,
       LEVEL c071, LEVEL c072, LEVEL c073, LEVEL c074, LEVEL c075, LEVEL c076, LEVEL c077, 
       LEVEL c078, LEVEL c079, LEVEL c080, LEVEL c081, LEVEL c082, LEVEL c083, LEVEL c084, 
       LEVEL c085, LEVEL c086, LEVEL c087, LEVEL c088, LEVEL c089, LEVEL c090, LEVEL c091, 
       LEVEL c092, LEVEL c093, LEVEL c094, LEVEL c095, LEVEL c096, LEVEL c097, LEVEL c098, 
       LEVEL c099, LEVEL c100, LEVEL c101, LEVEL c102, LEVEL c103, LEVEL c104, LEVEL c105, 
       LEVEL c106, LEVEL c107, LEVEL c108
  FROM DUAL
CONNECT BY LEVEL <= 10
</pre>
<span style="font-weight:bold; font-style:italic">
Get the COLUMN_ID</span>

This is the column that you want to modify that isn't currently displayed on the Reports Attributes page
<pre class="brush: sql;">
SELECT column_id,
       column_alias,
       display_order,
       report_label
  FROM APEX_APPLICATION_PAGE_IR_COL
 WHERE application_id = :app_id
   AND page_id = :page_id
</pre><span style="font-weight:bold; font-style:italic">
Run</span>

Go to the Reports Attributes screen (the screen that lists all the IR columns)

Open Firebug and go to the Console Window. Enter the following:
<pre class="brush: js;">
apex_p601_setColumnIDandSubmit('311507131164520006'); // Enter your column ID here
</pre>