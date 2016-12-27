---
title: 'APEX: How to Pass Multiselect List Values in URL'
tags:
  - APEX
date: 2009-07-28 09:00:00
alias:
---

When passing multiselect list values, or any multi LOV, in the URL you may have some unexpected behaviors. Here's an example: [http://apex.oracle.com/pls/otn/f?p=20195:2100](http://apex.oracle.com/pls/otn/f?p=20195:2100)

If you take a look at the example you'll notice that the URL doesn't contain all the values that you may have submitted. For example I selected KING (7839), BLAKE (7698), and CLARK (7782). I would expect the URL to contain these values when I pass them via the URL. Instead the URL looks like this: 
> <span style="font-style:italic;">http://apex.oracle.com/pls/otn/f?p=20195:2100:1674288126968745::NO::<span style="font-weight:bold;">P2100_EMPNO_LIST:7839:7698</span></span>
Notice how only 2 values are passed in? That's because the delimiter used in LOVs is the same that is used in the URL. What can be even more confusing is that I selected 3 values but when I pass them in the URL only 1 is "accepted". This is because the last value in the URL is the "PrinterFriendly" parameter (please see: [http://download.oracle.com/docs/cd/E14373_01/appdev.32/e11838/concept.htm#BEIJCIAG](http://download.oracle.com/docs/cd/E14373_01/appdev.32/e11838/concept.htm#BEIJCIAG))

To fix the issue for all your mutli LOVs you can use a similar technique that I used to resolve the [%null% issue](http://apex-smb.blogspot.com/2009/07/how-to-resolve-null-issue-in-apex-lovs.html). An example of the fix can be found here: [http://apex.oracle.com/pls/otn/f?p=20195:2110](http://apex.oracle.com/pls/otn/f?p=20195:2110). If you take a look at the example and select several employees the URL now looks like this:
> http://apex.oracle.com/pls/otn/f?p=20195:2110:1674288126968745::NO::<span style="font-weight:bold;">P2110_EMPNO_LIST:7839*7698*7782</span>
Notice how the delimiters are *s for the empnos?

1- Create Application Process to replace colon delimiter with *
<span style="font-style:italic;">Note: You aren't limited to using * as your delimiter</span>

Name: AP_REMOVE_URL_DELIM_FROM_ITEMS
Sequence: -10   (helps ensure that it is run before any other process
Point: On Submit: After Page Submission - Before Computations and Validations

<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT item_name,
                   REPLACE (v (item_name), ':', '*') new_item_value
              FROM (SELECT item_name
                      FROM apex_application_page_items aapi
                     WHERE aapi.application_id = :app_id
                       AND aapi.page_id = :app_page_id
                       AND LOWER (display_as) IN ('checkbox', 'shuttle', 'select list', 'multiselect list') -- Limiting to these types. Can remove if you want to handle all types
                       AND ROWNUM > 0) x
             WHERE INSTR (v (x.item_name), ':') > 0) LOOP
    apex_util.set_session_state (x.item_name, x.new_item_value);
  END LOOP;
END;
</pre>

<span style="font-style:italic;color:red;">Note: This will replace the colon delimiter with a *. This may change some of your validations, page processes etc.</span>

2- Create Application Process to replace * with colon delimiter on page load

Name: AP_RESET_URL_DELIM_FROM_ITEMS
Sequence: -10   (helps ensure that it is run before any other process)
Point: On Load: Before Header (page template header)

<pre class="brush: sql">
BEGIN
  FOR x IN
    (SELECT item_name,
            REPLACE (v (item_name), '*', ':') new_item_value
       FROM (SELECT item_name
               FROM apex_application_page_items aapi
              WHERE aapi.application_id = :app_id
                AND aapi.page_id = :app_page_id
                AND LOWER (display_as) IN
                      ('checkbox', 'shuttle', 'select list', 'multiselect list')   -- Limiting to these types. Can remove if you want
                AND ROWNUM > 0) x
      WHERE INSTR (v (x.item_name), '*') > 0) LOOP
    apex_util.set_session_state (x.item_name, x.new_item_value);
  END LOOP;
END;
</pre>