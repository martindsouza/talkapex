---
title: APEX Region Errors - Part 2
tags:
  - apex
date: 2010-10-19 07:50:00
alias:
---

<span style="font-weight:bold;color:red;">Disclaimer: </span><span style="color:red;">This is an advanced post that discusses and modifies some of the inner workings of APEX.</span>

In my previous post on [APEX Region Errors](http://www.talkapex.com/2010/10/apex-region-errors-part-1.html) I wrote about logging region errors in APEX. In this post I'll cover how to automatically be notified when a region error occurs and store some additional log information.

It's important that you actually read my previous posts on [APEX Region Errors](http://www.talkapex.com/2010/10/apex-region-errors-part-1.html) and  [Custom Error Messages in APEX](http://www.talkapex.com/2010/09/custom-error-messages-in-apex.html). The rest of this post will assume that you've read both of these posts.

Before we continue we need to review how the activity log works. APEX_WORKSPACE_ACTIVITY_LOG is a view which references APEX_040000\. WWV_FLOW_ACTIVITY_LOG. WWV_FLOW_ACTIVITY_LOG is a view which joins APEX_040000.WWV_FLOW_ACTIVITY_LOG1$ and APEX_040000.WWV_FLOW_ACTIVITY_LOG2$. The first paragraph of [APEX Logs: Storing Log Data](http://www.talkapex.com/2009/05/apex-logs-storing-log-data.html) provides a brief description of why the logs are stored in two tables.

In order to have advanced log information and notify an administrator when a region error occurs we will apply a trigger on each of the log tables.

<span style="font-weight:bold;color:red;">Disclaimer (again): </span><span style="color:red;">Modifying anything in the APEX schema will put your APEX instance in an unsupported state. Please proceed with caution. I take no responsibility for any negative outcomes about this.</span><span style="font-style:italic;"> [Joel Kallman](http://joelkallman.blogspot.com) wrote about [The Perils of Modifying the Application Express Metadata](http://joelkallman.blogspot.com/2010/01/perils-of-modifying-application-express.html) in regards to Oracle support.</span>

<span style="font-weight:bold;">- Install Logger and compile the sp_log_error_page procedure.</span>
Install logger and sp_log_error_page in the schema that your APEX application will run against.
Logger: [http://logger.samplecode.oracle.com](http://logger.samplecode.oracle.com/)
sp_log_error_page: [http://www.talkapex.com/2010/09/custom-error-messages-in-apex.html](http://www.talkapex.com/2010/09/custom-error-messages-in-apex.html)

<span style="font-weight:bold;">- Apply trigger to log tables</span>
Apply triggers to both of the activity log tables so that we can be notified right away when a page or region level error occurs.

As SYSTEM compile the following triggers:
<pre class="brush: sql">
CREATE OR REPLACE TRIGGER giffy.trg_apex_activity_log1_air -- Where "giffy" is the schema that I am running my APEX app in. Change accordingly
  AFTER INSERT
  ON apex_040000.wwv_flow_activity_log1$
  FOR EACH ROW
  WHEN (new.SQLERRM IS NOT NULL)
DECLARE
  v_count                       PLS_INTEGER;
BEGIN
  -- Check if the application belongs to this schema
  SELECT COUNT (application_id)
    INTO v_count
    FROM apex_applications
   WHERE application_id = :new.flow_id
     AND owner = 'GIFFY'; -- Schema that I am running in

  IF v_count > 0 THEN
    sp_log_error_page (p_scope_prefix => 'apex.region_error',
                       p_application_id => :new.flow_id,
                       p_page_id   => :new.step_id,
                       p_oracle_err_msg => :new.SQLERRM,
                       p_apex_err_msg => '{component_type: ' || :new.sqlerrm_component_type || ', component_name: ' || :new.sqlerrm_component_name || '}',
                       p_email     => NULL);
  END IF;
END;
</pre>
And... (differences are highlighted)
<pre class="brush: sql; highlight: [1,3]">
CREATE OR REPLACE TRIGGER giffy.trg_apex_activity_log2_air -- Where "giffy" is the schema that I am running my APEX app in. Change accordingly
  AFTER INSERT
  ON apex_040000.wwv_flow_activity_log2$
  FOR EACH ROW
  WHEN (new.SQLERRM IS NOT NULL)
DECLARE
  v_count                       PLS_INTEGER;
BEGIN
  -- Check if the application belongs to this schema
  SELECT COUNT (application_id)
    INTO v_count
    FROM apex_applications
   WHERE application_id = :new.flow_id
     AND owner = 'GIFFY'; -- Schema that I am running in

  IF v_count > 0 THEN
    sp_log_error_page (p_scope_prefix => 'apex.region_error',
                       p_application_id => :new.flow_id,
                       p_page_id   => :new.step_id,
                       p_oracle_err_msg => :new.SQLERRM,
                       p_apex_err_msg => '{component_type: ' || :new.sqlerrm_component_type || ', component_name: ' || :new.sqlerrm_component_name || '}',
                       p_email     => NULL);
  END IF;
END;
</pre>

Now when an page or region level error occurs you will have advanced logging information and you have the option to be notified by email. The log information will be stored in your schema in the <span style="font-style:italic;">LOGGER_LOGS</span> table

[APEX Region Errors - Part 3](http://www.talkapex.com/2011/07/apex-region-errors-part-3.html)
