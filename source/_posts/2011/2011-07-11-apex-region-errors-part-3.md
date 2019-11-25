---
title: APEX Region Errors - Part 3
tags:
  - apex
date: 2011-07-11 07:00:00
alias:
---

<span style="color:red;"><span style="font-weight:bold;">Disclaimer</span>: This is an advanced post that discusses and modifies some of the inner workings of APEX.</span>

In [APEX Region Errors - Part 2](http://www.talkapex.com/2010/10/apex-region-errors-part-2.html) I discussed how to add triggers on the APEX Activity Log tables to store information in custom error tables when a user encounters an APEX region error.

Instead of storing the information in custom error tables you can leverage the APEX Feedback tool and trigger an automatic feedback entry. This may be a preferred option as you don't need to create custom tables and the feedback tool provides a lot of information.

Before continuing it's important that you know how to use the new APEX Feedback tool. If you don't know about the APEX Feedback tool I suggest that you read about how to create the feedback link ([http://dgielis.blogspot.com/2010/03/apex-40-feedback-link.html](http://dgielis.blogspot.com/2010/03/apex-40-feedback-link.html)) and how to access the feedback tool ([http://dgielis.blogspot.com/2010/03/apex-40-looking-at-feedback-through.html](http://dgielis.blogspot.com/2010/03/apex-40-looking-at-feedback-through.html)). Try implementing it in a dummy application to see what you can do with it.

The following triggers will enter a feedback entry each time a region error occurs:

<span style="font-style:italic; color:red;"><span style="font-weight:bold;">Disclaimer (again)</span>: Modifying anything in the APEX schema will put your APEX instance in an unsupported state. Please proceed with caution. I take no responsibility for any negative outcomes from this.</span>
<pre class="brush: sql">
-- NOTE: Do this as the APEX_040000 user or a privlidged user such as SYSTEM
CREATE OR REPLACE TRIGGER apex_040000.trg_apex_activity_log1_air
  AFTER INSERT
  ON apex_040000.wwv_flow_activity_log1$
  FOR EACH ROW
  WHEN (new.SQLERRM IS NOT NULL)
DECLARE
BEGIN
  -- Log as feedback
  apex_util.submit_feedback (
    p_comment         => 'AUTO MSG: Region Error', -- Put a comment here that can be used to easily identify auto generated feedback messages
    p_type            => 3, -- Bug. See API documentation for different values
    p_application_id  => :new.flow_id,
    p_page_id         => :new.step_id,
    p_email           => null,
    p_label_01        => 'Error Message', -- You can add up to 8 label/attributes. See API documentation for more information
    p_attribute_01    => :new.sqlerrm,
    p_label_02        => 'Component Type',
    p_attribute_02    => :new.sqlerrm_component_type,
    p_label_03        => 'Component Name',
    p_attribute_03    => :new.sqlerrm_component_name
    );

   -- Could use APEX_MAIL to send a notification to a list of developers to take a look at problem
   -- This is entirely optional. Modify as required
   apex_mail.send(
    p_to => 'someone@yourorg.com',
    p_from => 'someone@yourorg.com',
    p_body => 'Region Error Occured. Please Check Feedback',
    p_body_html => '',
    p_subj => 'APEX Region Error');

END;
/</pre>And... (differences are highlighted)
<pre class="brush: sql; highlight: [2,4]">
-- NOTE: Do this as the APEX_040000 user or a privlidged user such as SYSTEM
CREATE OR REPLACE TRIGGER apex_040000.trg_apex_activity_log2_air
  AFTER INSERT
  ON apex_040000.wwv_flow_activity_log2$
  FOR EACH ROW
  WHEN (new.SQLERRM IS NOT NULL)
DECLARE
BEGIN
  -- Log as feedback
  apex_util.submit_feedback (
    p_comment         => 'AUTO MSG: Region Error', -- Put a comment here that can be used to easily identify auto generated feedback messages
    p_type            => 3, -- Bug. See API documentation for different values
    p_application_id  => :new.flow_id,
    p_page_id         => :new.step_id,
    p_email           => null,
    p_label_01        => 'Error Message', -- You can add up to 8 label/attributes. See API documentation for more information
    p_attribute_01    => :new.sqlerrm,
    p_label_02        => 'Component Type',
    p_attribute_02    => :new.sqlerrm_component_type,
    p_label_03        => 'Component Name',
    p_attribute_03    => :new.sqlerrm_component_name
    );

   -- Could use APEX_MAIL to send a notification to a list of developers to take a look at problem
   -- This is entirely optional. Modify as required
   apex_mail.send(
    p_to => 'someone@yourorg.com',
    p_from => 'someone@yourorg.com',
    p_body => 'Region Error Occured. Please Check Feedback',
    p_body_html => '',
    p_subj => 'APEX Region Error');

END;
/</pre>When a region error occurs you can now view the information in Team Development > Feedback.

[![](http://1.bp.blogspot.com/-uNwLAxFt6PU/ThYGRbJX56I/AAAAAAAAD8s/1mRG91GGGrw/s400/auto_err_msg_feedback.jpg)](http://1.bp.blogspot.com/-uNwLAxFt6PU/ThYGRbJX56I/AAAAAAAAD8s/1mRG91GGGrw/s1600/auto_err_msg_feedback.jpg)If you click the <span style="font-style:italic;">Edit</span> button you can get a lot of detailed information about was happening when the user encountered the error including all of the values in session state.

[![](http://3.bp.blogspot.com/-TWL3FrTM1bY/ThYJTD6SEuI/AAAAAAAAD80/oGArcyUL1yE/s400/auto_err_msg_feedback_dtl.jpg)](http://3.bp.blogspot.com/-TWL3FrTM1bY/ThYJTD6SEuI/AAAAAAAAD80/oGArcyUL1yE/s1600/auto_err_msg_feedback_dtl.jpg)
