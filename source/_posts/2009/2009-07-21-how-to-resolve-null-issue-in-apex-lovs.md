---
title: How to resolve %null% issue in APEX LOVs
tags:
  - apex
date: 2009-07-21 09:00:00
alias:
---

[Patrick Wolf](http://www.inside-oracle-apex.com) mentioned this at [ODTUG Kaleidoscope](http://www.odtugkaleidoscope.com) this year.

After you implement your first LOV in an APEX application you'll quickly learn about the %null% problem. APEX substitutes an empty string for <span style="font-style:italic;">Null return value</span> as %null%.

They're several workarounds, like using "<span style="font-weight:bold;">-1</span>" as the NULL value. Or modifying your query using "<span style="font-weight:bold;">'%' || 'null%'</span>". For example:

<pre class="brush: sql">
SELECT ename,
       empno
  FROM emp
 WHERE empno = DECODE (:p_empno, '%' || 'null%', empno, NULL, empno, :p_empno)
</pre>

Instead of using workarounds you can convert %null% to NULL (empty string) by creating the following application process:

<span style="font-weight:bold;">Application Process:</span> <span style="font-style:italic;">AP_SET_LOV_NULLS</span>
<span style="font-weight:bold;">Process Point:</span> <span style="font-style:italic;">On Submit - Before Computations and Validations </span>
<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT *
              FROM (SELECT item_name
                      FROM apex_application_page_items aapi
                     WHERE aapi.application_id = :app_id
                       AND aapi.page_id = :app_page_id
                       AND LOWER (aapi.lov_display_null) = 'yes'
                       AND aapi.lov_definition IS NOT NULL
                       AND aapi.lov_null_value IS NULL
                       AND ROWNUM > 0) x
             WHERE LOWER (v (x.item_name)) = '%' || 'null%') LOOP
    apex_util.set_session_state (x.item_name, NULL);
  END LOOP;
END;
</pre>
