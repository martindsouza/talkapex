---
title: How to determine if user can view an APEX region.
tags:
  - APEX
date: 2009-05-21 07:30:00
alias:
---

<span style="font-style:italic">If you just want to see how to determine if user is allowed to view a region scroll to bottom of this post, however I suggest you read the explanation as to why/when you may want to use this function</span>

I recently received a request to build an internal application to generate many reports for one of the teams in my organization. At a high level, the application is supposed to have (it's still not built) several "Report Result" pages. Each Report Result page will contain 10 to 15 reports. Their will be a menu page in which end users can select the Report Result page they'd like to run, and the individual reports they want to run on that page: 

[![](http://2.bp.blogspot.com/_33EF80fk9sM/ShTbH_xs65I/AAAAAAAADpA/VWfpxtTCg_A/s400/_16_screen_shot.bmp)](http://2.bp.blogspot.com/_33EF80fk9sM/ShTbH_xs65I/AAAAAAAADpA/VWfpxtTCg_A/s1600-h/_16_screen_shot.bmp)

Query for Checkboxes:

<pre class="brush: sql">
SELECT region_name d,
       region_id r
  FROM apex_application_page_regions
 WHERE application_id = 103
   AND page_id = 2
ORDER BY UPPER (d) ASC   
</pre>

When users click "Go", they will go to the selected "Report Result" page and execute the selected reports.

So far this is fairly straight forward (using the [APEX Dictionary](http://apex-smb.blogspot.com/2008/11/how-to-list-apex-dictionary-views-using.html) to help out). To make things more difficult, certain reports should only be accessible depending on the parsing schema. For example if the parsing schema is "SCOTT" then Report/Region 2 should not be displayed. 

To meet this requirement I can add a condition (SQL Exists) to the Report 2 region on Page 2:

<pre class="brush: sql">
SELECT 1
  FROM DUAL
 WHERE :p2_region_static_id_list IS NULL
    OR (    :f_parsing_schema != 'SCOTT' -- F_PARSING_SCHEMA is an application_item I added
        AND INSTR (':' || 'REPORT2' || ':', :p2_region_static_id_list) > 0)
    -- "REPORT2" is the Region's Static ID
</pre>    

When I run Page 2, Reports 1, 3, and 4 are executed which is correct. However, on the menu page the user had the option to select Report 2 when they should have never been allowed to see it in the check list since the parsing schema was SCOTT. 

To resolve this issue, I've created the following function to determine if a user can view a specified region:

<pre class="brush: sql">
/**
 * Determine if current user has permissions to view region
 * @param p_region_id region to test
 * @return Y or N
 * @author Martin Giffy DSouza - http://apex-smb.blogspot.com/
 */

CREATE OR REPLACE FUNCTION f_apex_permission_flag (
  p_region_id IN apex_application_page_regions.region_id%TYPE
)
  RETURN VARCHAR2
AS
  TYPE v_apex_region_rec_type IS RECORD (
    page_authorization_scheme apex_application_page_regions.authorization_scheme%TYPE,
    page_build_option_status apex_application_build_options.build_option_status%TYPE,
    reg_authorization_scheme apex_application_page_regions.authorization_scheme%TYPE,
    reg_build_option_status apex_application_build_options.build_option_status%TYPE,
    reg_condition_expression1 apex_application_page_regions.condition_expression1%TYPE,
    reg_condition_expression2 apex_application_page_regions.condition_expression2%TYPE,
    reg_condition_type apex_standard_conditions.d%TYPE
  );

  v_apex_region v_apex_region_rec_type;
BEGIN
  -- If region is null return N to access
  IF p_region_id IS NULL THEN
    RETURN 'N';
  END IF;

  SELECT aap.authorization_scheme page_authorization_scheme,
         aap_bo.build_option_status page_build_option_status,
         aapr.authorization_scheme reg_authorization_scheme,
         aapr_bo.build_option_status reg_build_option_status,
         aapr.condition_expression1 reg_condition_expression1,
         aapr.condition_expression2 reg_condition_expression2,
         asc_reg.d reg_condition_type
    INTO v_apex_region
    FROM apex_application_pages aap,
         apex_application_build_options aap_bo,
         apex_application_build_options aapr_bo,
         apex_application_page_regions aapr,
         apex_standard_conditions asc_reg
   WHERE aapr.region_id = p_region_id
     AND aapr.page_id = aap.page_id
     AND aapr.application_id = aap.application_id
     AND UPPER (aap.build_option) = UPPER (aap_bo.build_option_name(+))
     AND aapr.build_option_id = aapr_bo.build_option_id(+)
     AND aapr.condition_type = asc_reg.r(+);

  -- PAGE VALIDATIONS
  -- Check Page Build Option
  IF UPPER (NVL (v_apex_region.page_build_option_status, 'INCLUDE')) != 'INCLUDE' THEN
    RETURN 'N';
  END IF;

  -- Check Page Authorization
  IF v_apex_region.page_authorization_scheme IS NOT NULL THEN
    IF apex_util.public_check_authorization (p_security_scheme => v_apex_region.page_authorization_scheme) = FALSE THEN
      RETURN 'N';
    END IF;
  END IF;

  -- REGION VALIDATIONS

  -- Check Region Build Option
  IF UPPER (NVL (v_apex_region.reg_build_option_status, 'INCLUDE')) != 'INCLUDE' THEN
    RETURN 'N';
  END IF;

  -- Check Region Authorization
  IF v_apex_region.reg_authorization_scheme IS NOT NULL THEN
    IF apex_util.public_check_authorization (p_security_scheme => v_apex_region.reg_authorization_scheme) = FALSE THEN
      RETURN 'N';
    END IF;
  END IF;

  -- Check the region condition
  IF v_apex_region.reg_condition_type IS NOT NULL THEN
    IF wwv_flow_conditions.standard_condition (p_condition_type     => v_apex_region.reg_condition_type,
                                               p_condition          => v_apex_region.reg_condition_expression1,
                                               p_condition2         => v_apex_region.reg_condition_expression1
                                              ) = FALSE THEN
      RETURN 'N';
    END IF;
  END IF;

  -- All test passed
  RETURN 'Y';
END f_apex_permission_flag;
</pre>

Using the new function, the Checkbox Query (on Page 1) now becomes:

<pre class="brush: sql">
SELECT   region_name d,
         region_id r
    FROM (SELECT region_id,
                 region_name,
                 f_apex_permission_flag (region_id) permission_flag
            FROM apex_application_page_regions
           WHERE application_id = 103
             AND page_id = 2) x
   WHERE x.permission_flag = 'Y'
ORDER BY UPPER (d) ASC
</pre>

<span style="font-style:italic; color:red;">A word of precaution: If you're using this function to verify region access on another page you should be aware of how the condition is defined. If it is referencing items specific to its own page then you may get some false positives as those items may not have been defined yet.</span>