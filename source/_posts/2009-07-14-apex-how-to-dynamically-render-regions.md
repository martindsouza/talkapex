---
title: 'APEX: How to Dynamically Render Regions'
tags:
  - APEX
date: 2009-07-14 07:30:00
alias:
---

Suppose you needed to enable and disable report regions on each page based on a parameter. You could add a condition to each region. If the conditions were all the same, the smart thing to do would be to create a function and have your condition reference the function.

What if your application had 100 pages? Would you remember to apply the condition to each report region in the 100 pages?

This is not a problem that most developers run into, however when you are building large applications something similar may come up. If you can find a way to dynamically control items, regions, processes, etc this can save on development time.

At the [ODTUG Kaleidoscope](http://www.odtugkaleidoscope.com) conference Dennis Vanill gave a presentation on how to use Page 0 items to enable and disable APEX objects dynamically. Using this logic, here's an example on how to dynamically disable a region. 

<span style="font-style:italic;color:red">Note: Use this when appropriate. For basic conditions stick with using "regular" conditions</span>

A demo is available here: [http://apex.oracle.com/pls/otn/f?p=20195:1900](http://apex.oracle.com/pls/otn/f?p=20195:1900)

1- Create a page with some report regions
<pre class="brush: sql">
-- Interactive Report:
SELECT *
FROM emp

-- Regular Report
SELECT ename, sal
FROM emp
</pre>

2- Create Page Process: On Load - Before Header
<pre class="brush: sql">
DECLARE
BEGIN
  IF NVL (:p1900_hide_reports_flag, 'N') = 'Y' THEN
    FOR x IN (SELECT region_id
                FROM apex_application_page_regions
               WHERE application_id = :app_id
                 AND page_id = :app_page_id
                 AND source_type IN ('Report', 'Interactive Report')) LOOP
      FOR i IN 1 .. apex_application.g_plug_id.COUNT LOOP
        IF apex_application.g_plug_id (i) = x.region_id THEN
          apex_application.g_plug_display_condition_type (i) := 'NEVER';
        END IF;
      END LOOP;
    END LOOP;
  END IF;
END;
</pre>

2- (For Demo purposes only)
I added the following on Page 0 to display in the example application. This shows that no conditions were applied to a region
<pre class="brush: sql">
SELECT region_id,
       region_name,
       source_type,
       condition_type,
       condition_expression1,
       condition_expression2,
       build_option,
       authorization_scheme
  FROM apex_application_page_regions
 WHERE application_id = :app_id
   AND page_id = :app_page_id
</pre>   

You can use the same logic to control computations, items, etc. Take a look at apex_application (<span style="font-style:italic;">desc apex_application</span>) for more options.