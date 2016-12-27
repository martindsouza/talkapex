---
title: How to Automatically Remove Items and Values in APEX URLs
tags:
  - APEX
  - ORACLE APEX
date: 2010-01-03 23:25:00
alias:
---

A while ago I wrote about how to pass multi-select lists through the URL: [http://apex-smb.blogspot.com/2009/07/apex-how-to-pass-multiselect-list.html](http://apex-smb.blogspot.com/2009/07/apex-how-to-pass-multiselect-list.html). This method works, however it will only support up to 4000 characters as a result of the functions it uses.

What happens when a user selects more than 4000 characters? You could update the processes to handle clobs etc. I took a different approach when faced with this problem. Instead of trying to update the process to handle clobs I asked: <span style="font-style: italic">Why am I passing the values through the URL?</span> Wouldn't it be easier if I didn't pass values through the URL?

When I first thought about this, I thought I could run a process on each page that would set the appropriate variables to the calling page. But then developers would have to look in the page branch and page processes to see what items were being passed to which pages. This sounded confusing and goes against the APEX framework. I really don't like having developers do extra work when they don't need to or go against standards already put in place.

I took a different approach to this problem. Instead of having a special page process to pass values from one page to another I was able to "intercept" the page branch and modify it before it was executed. I did this by taking the page branch and manually setting the item/value pairs, then removing them from the page branch. Here's an example of the final solution: [http://apex.oracle.com/pls/otn/f?p=20195:2800](http://apex.oracle.com/pls/otn/f?p=20195:2800).

Here are some screen shots from the demo application:

<span style="font-style:italic">Page Branch configuration:</span>

[![](http://4.bp.blogspot.com/_33EF80fk9sM/S0F9YM_k10I/AAAAAAAADv8/rNCZa9Wi7AM/s400/url_pass_param_03.png)](http://4.bp.blogspot.com/_33EF80fk9sM/S0F9YM_k10I/AAAAAAAADv8/rNCZa9Wi7AM/s1600-h/url_pass_param_03.png)
<span style="font-style:italic">Passing in values via the URL (note that I'd like "X" to be "abc:def", however it's just abc)</span>

[![](http://1.bp.blogspot.com/_33EF80fk9sM/S0F9X4aiEII/AAAAAAAADv0/2_FTajRMH-4/s400/url_pass_param_02.png)](http://1.bp.blogspot.com/_33EF80fk9sM/S0F9X4aiEII/AAAAAAAADv0/2_FTajRMH-4/s1600-h/url_pass_param_02.png)
<span style="font-style:italic;">Running the page process to manually remove the item/value pairs from the URL:</span>

[![](http://4.bp.blogspot.com/_33EF80fk9sM/S0F9XXhr8JI/AAAAAAAADvs/Wq2Vmvuc1wA/s400/url_pass_param_01.png)](http://4.bp.blogspot.com/_33EF80fk9sM/S0F9XXhr8JI/AAAAAAAADvs/Wq2Vmvuc1wA/s1600-h/url_pass_param_01.png)
Before reviewing the code, it's important to note the following assumptions. I made these assumptions to make the code demo easier to read.
<span style="font-style: italic">
-   Only 1 branch is defined on the page with no conditions etc.
-   The branch only contains at most 1 clear cache
-   The branch passes as most 1 item/value pair to the other page
</span>
<span style="font-weight: bold">- Create page process: Remove URL Params</span>
- Branch Point: On Submit After Processing

<span style="font-style: italic">Note: You could easily turn this into an application process to be run throughout your application</span>
<pre class="brush: sql">
-- NOTE: For the purpose of this example I'm assuming the following:
--   Only 1 branch is defined with no conditions etc.
--   The branch only contains at most 1 clear cache
--   The branch passes as most 1 item/value pair to the other page

DECLARE
   v_branch_action   apex_application_page_branches.branch_action%TYPE;
   p_app_id          apex_application_page_branches.application_id%TYPE := :app_id;
   p_page_id         apex_application_page_branches.page_id%TYPE := :app_page_id;
   v_clear_cache     NUMBER;
   v_item_names      VARCHAR2 (4000);
   v_item_values     VARCHAR2 (4000);
BEGIN
   -- Get branch action
   -- The branch action is the URL before it is parsed by APEX
   SELECT branch_action
     INTO v_branch_action
     FROM apex_application_page_branches
    WHERE application_id = p_app_id AND page_id = p_page_id;

   -- Get the clear cache options
   v_clear_cache :=
      SUBSTR (v_branch_action,
              INSTR (v_branch_action, ':', 1, 5) + 1,
                INSTR (v_branch_action, ':', 1, 6)
              - INSTR (v_branch_action, ':', 1, 5)
              - 1
             );

   -- Manually clear the cache if required
   IF v_clear_cache IS NOT NULL
   THEN
      apex_util.clear_page_cache (p_page_id => v_clear_cache);
   END IF;

   -- Get Page Param values
   v_item_names :=
      SUBSTR (v_branch_action,
              INSTR (v_branch_action, ':', 1, 6) + 1,
                INSTR (v_branch_action, ':', 1, 7)
              - INSTR (v_branch_action, ':', 1, 6)
              - 1
             );
   v_item_values :=
      SUBSTR (v_branch_action,
              INSTR (v_branch_action, ':', 1, 7) + 1,
                INSTR (v_branch_action, '&success_msg=', 1, 1)
              - INSTR (v_branch_action, ':', 1, 7)
              - 1
             );

   -- If item/value pairs exist, manually set the values
   IF v_item_names IS NOT NULL
   THEN
      -- See: http://apex-smb.blogspot.com/2009/07/apexapplicationdosubstitutions.html for more info on apex_application.do_substitutions
      apex_util.set_session_state
           (p_name       => v_item_names,
            p_value      => apex_application.do_substitutions (TRIM (v_item_values))
           );
   END IF;

   -- Modify the branch action
   -- Remove the clear cache option if applicable
   IF v_clear_cache IS NOT NULL
   THEN
      v_branch_action :=
            SUBSTR (v_branch_action, 1, INSTR (v_branch_action, ':', 1, 5))
         || SUBSTR (v_branch_action, INSTR (v_branch_action, ':', 1, 6));
   END IF;

   -- Remove the item name/value pairs if applicable
   IF v_item_names IS NOT NULL
   THEN
      -- Remove item Names
      v_branch_action :=
            SUBSTR (v_branch_action, 1, INSTR (v_branch_action, ':', 1, 6))
         || SUBSTR (v_branch_action, INSTR (v_branch_action, ':', 1, 7));
      -- Remove item values
      v_branch_action :=
            SUBSTR (v_branch_action, 1, INSTR (v_branch_action, ':', 1, 7))
         || SUBSTR (v_branch_action,
                    INSTR (v_branch_action, '&success_msg=', 1, 1)
                   );
   END IF;

   -- Set the new branch action
   apex_application.g_branch_action (1) := v_branch_action;
END;
</pre>