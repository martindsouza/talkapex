---
title: 'APEX Logs: Storing Mail Log Data'
tags:
  - APEX
  - ORACLE
  - ORACLE APEX
date: 2010-04-13 09:00:00
alias:
---

A while ago (almost a year ago) I wrote about how to permanently store APEX logs into your own tables ([http://apex-smb.blogspot.com/2009/05/apex-logs-storing-log-data.html](http://apex-smb.blogspot.com/2009/05/apex-logs-storing-log-data.html)). I covered the APEX_WORKSPACE_ACCESS_LOG and APEX_WORKSPACE_ACTIVITY_LOG logs. I forgot to include how to permanently store the APEX_MAIL_LOG. You'll only need to do this if you use APEX_MAIL to send email.

<span style="font-weight:bold;">1- Create the APEX Mail Log table</span>
<pre class="brush: sql">
-- Mail Log
CREATE TABLE tapex_mail_log
AS  SELECT * FROM apex_mail_log;
</pre><span style="font-weight:bold;">2- Update the APEX Mail Log table</span>

<span style="font-style:italic;">Note: You may want to store this in a procedure and run as a nightly scheduled job so you don't forget to update the tables</span>
<pre class="brush: sql">
BEGIN
  -- apex_mail_log is linked to the workspace.
  -- Need to access all the mail sent for each workspace that is linked to this schema

  FOR x IN (SELECT workspace_id FROM apex_workspaces) LOOP
    -- Set the workspace ID
    wwv_flow_api.set_security_group_id (x.workspace_id);

    INSERT INTO tapex_mail_log (mail_to,
                                mail_from,
                                mail_replyto,
                                mail_subj,
                                mail_cc,
                                mail_bcc,
                                mail_send_error,
                                last_updated_on)
      SELECT aml.mail_to,
             aml.mail_from,
             aml.mail_replyto,
             aml.mail_subj,
             aml.mail_cc,
             aml.mail_bcc,
             aml.mail_send_error,
             aml.last_updated_on
        FROM tapex_mail_log x, apex_mail_log aml
       WHERE     NVL (aml.mail_to, -1) = NVL (x.mail_to(+), -1)
             AND NVL (aml.mail_from, -1) = NVL (x.mail_from(+), -1)
             AND NVL (aml.mail_replyto, -1) = NVL (x.mail_replyto(+), -1)
             AND NVL (aml.mail_subj, -1) = NVL (x.mail_subj(+), -1)
             AND NVL (aml.mail_cc, -1) = NVL (x.mail_cc(+), -1)
             AND NVL (aml.mail_bcc, -1) = NVL (x.mail_bcc(+), -1)
             AND NVL (aml.mail_send_error, -1) = NVL (x.mail_send_error(+), -1)
             AND aml.last_updated_on = x.last_updated_on(+)
             AND x.ROWID IS NULL;
  END LOOP;
END;
</pre>