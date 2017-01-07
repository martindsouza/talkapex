---
title: Saving Saved Interactive Reports when Updating Application
tags:
  - apex
date: 2009-10-26 08:25:00
alias:
---

<span style="font-style:italic;color:red;">Their is now a supported method to preserve saved interactive reports. Please read the following post which explains how to do this in APEX 4.0: [http://joelkallman.blogspot.com/2010/07/where-did-my-saved-interactive-reports.html](http://joelkallman.blogspot.com/2010/07/where-did-my-saved-interactive-reports.html)</span>

[![](http://4.bp.blogspot.com/_33EF80fk9sM/SuU1P0nNQOI/AAAAAAAADtQ/ElNfSXkuM04/s320/save_key_small.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/SuU1P0nNQOI/AAAAAAAADtQ/ElNfSXkuM04/s1600-h/save_key_small.jpg)
When updating existing APEX applications that contain Interactive Reports (IR) you may, not knowningly, delete users saved IRs. The only supported way to prevent this from happening is to ensure that your Application ID is the same when you move it from Dev, to Test, to Production. [David Peake](http://dpeake.blogspot.com) wrote a full explanation of this issue here: [http://dpeake.blogspot.com/2009/01/preserving-user-saved-interactive.html](http://dpeake.blogspot.com/2009/01/preserving-user-saved-interactive.html). I suggest you read his post before continuing.

What if you develop a single application that needed to be deployed to multiple clients/instances? I.e. you develop your application in DEV (100) and deploy to PROD (200), PROD (300), and PROD (400). Currently there's no supported way of doing this while maintaining your saved interactive reports.

The following script can be  run after updating your production applications to ensure that your saved IRs don't get lost. <span style="color:red;font-weight:bold;">Please note that this is not supported by Oracle and can put your application in an unsupported state.</span> <span style="font-style:italic;color:red;">If you are not an advanced APEX developer I do not suggest using this as it may result in unexpected results.</span>

Besides preserving saved IRs, users who are currently on the system will retain their current IR configurations, otherwise they will be lost. For example a user is working on an IR and applys some filters to it, you then update the application which will cause the interactive_report_id to change. The next time the user refreshes the page they won't see their filters any more (i.e. they'll have the default IR again). Running this script will prevent this from happening.

<span style="font-style:italic;">Note: this must be run as SYSTEM or a user with SYSTEM level access</span>
<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT a.ID, a.name, a.session_id,
                   b.interactive_report_id
              FROM apex_030200.wwv_flow_worksheet_rpts a,  -- This could also be flows...
                   apex_application_page_ir b
             WHERE a.flow_id = :app_id
               AND a.page_id = b.page_id -- Linking is done via the page so please be aware of any IR page changes
               AND b.application_id = a.flow_id
               AND a.worksheet_id != b.interactive_report_id
               and a.status = 'PRIVATE'
               )
  LOOP
    UPDATE apex_030200.wwv_flow_worksheet_rpts
       SET worksheet_id = x.interactive_report_id
     WHERE ID = x.ID;

  END LOOP;
END;
</pre>
