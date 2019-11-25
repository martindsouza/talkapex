---
title: Custom Error Messages in APEX
tags:
  - apex
date: 2010-09-12 23:32:00
alias:
---

As developers we try to prevent unhandled exceptions from occurring for end users. They can occur in any program or language, and APEX is no exception (pardon the pun).

When an unhandled exception happens, users are presented with an error message which is similar to the following:

[![](http://1.bp.blogspot.com/_33EF80fk9sM/TI2y4xmOtWI/AAAAAAAADz4/FlxLvbSxVgg/s400/error_msg_example.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TI2y4xmOtWI/AAAAAAAADz4/FlxLvbSxVgg/s1600/error_msg_example.jpg)
This error message isn't very user friendly and most users won't know what an "ORA-..." message means. The other issue with this screen is that it does not provide any feedback to developers. If the user does not report this issue developers won't know that it is happening.

As part of my [ODTUG presentation](http://www.talkapex.com/2010/09/odtug-how-to-be-creative.html) I demonstrated how to alter the default error page to provide a user friendly error message and provide an instant notification to developers that an error has occurred. This post will describe how to do implement a user friendly error handling method in APEX 4.0.

<span style="font-style:italic;">Special thanks to [Roel Hartman](http://roelhartman.blogspot.com/) and [Learco  Brizzi](http://learco-brizzi.blogspot.com/) for providing the ideas behind this.
</span>
<span style="font-weight:bold;">- Install Logger</span>
Logger is an open source package written by [Tyler Muth](http://tylermuth.wordpress.com). It's an excellent tool to quickly allow developers to instrument their code. Though it is not required, this demo references it. A copy of logger is available here: [http://logger.samplecode.oracle.com/](https://logger.samplecode.oracle.com/).

<span style="font-weight:bold;">- Install Simple Modal Plug-in</span>
Install the Simple Modal plugin into your APEX application: [http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html](http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html). When you download this plugin, the zip file contains 2 plugins. One to show the modal window and one to close the modal window. It is recommended that you install both if you plan to use it in other applications. Only the "Simple Modal - Show" plug is required for this demo.

<span style="font-weight:bold;">- Create Error Procedure</span>
Compile this procedure in your schema. It will log all the page items, application items, and all other not-null page items.
<pre class="brush: sql">
/**
 * Logs unhandled error message to Database
 * Logs:
 * - APEX and Oracle error messages
 * - All application level items
 * - All page items for p_page_id
 * - Items on other pages that are not null
 *
 * Logs are stored in logger_logs
 * Requires: https://logger.samplecode.oracle.com/
 *
 * @param p_scope_prefix Scope prefix used in logger
 * @param p_application_id
 * @param p_page_id Page that error occured on
 * @param p_oracle_error_msg Oracle error message
 * @param p_apex_error_msg APEX error message
 * @param p_email Email to be notified of error. If null, then no notification email sent.
 * @author Martin Giffy D''Souza http://www.talkapex.com
 */

CREATE OR REPLACE PROCEDURE sp_log_error_page (p_scope_prefix IN VARCHAR2,
                                               p_application_id IN apex_applications.application_id%TYPE DEFAULT v ('APP_ID'),
                                               p_page_id      IN apex_application_pages.page_id%TYPE,
                                               p_oracle_err_msg IN VARCHAR2 DEFAULT NULL,
                                               p_apex_err_msg IN VARCHAR2 DEFAULT NULL,
                                               p_email        IN VARCHAR2 DEFAULT NULL)
AS
  v_db_name                     VARCHAR2 (30);
  v_schema                      VARCHAR2 (30);
  v_scope                       VARCHAR2 (255);
BEGIN
  -- Set scope for logger
  v_scope := p_scope_prefix;

  -- Add Uniqe Identifier to scope
  v_scope := LOWER (v_scope || 'unhandeled_exception{session_id: ' || v ('APP_SESSION') || ', guid: ' || SYS_GUID () || '}');

  -- Log the initial error to be kept permanently
  logger.log_error ('Unhandeled Exception', v_scope, 'Oracle Error: ' || p_oracle_err_msg || CHR (10) || CHR (10) || 'APEX Error Page Message: ' || p_apex_err_msg);

  -- Log the information to help Dev team in production
  FOR x IN (SELECT 'APP_USER' item_name, v ('APP_USER') item_val FROM DUAL
            UNION
            SELECT 'APP_SESSION' item_name, v ('APP_SESSION') FROM DUAL
            UNION
            -- Include all the items from the current page
            SELECT item_name, v (item_name) item_val
              FROM apex_application_page_items
             WHERE application_id = p_application_id
               AND page_id = p_page_id
            UNION
            -- Include all the non-null page items
            SELECT item_name, item_val
              FROM (SELECT item_name, v (item_name) item_val
                      FROM apex_application_page_items
                     WHERE application_id = p_application_id
                       AND page_id != p_page_id)
             WHERE item_val IS NOT NULL
            UNION
            -- Include all Application Items
            SELECT item_name, v (item_name)
              FROM apex_application_items
             WHERE application_id = p_application_id)
  LOOP
    logger.log_information (x.item_name || ': ' || x.item_val, v_scope);
  END LOOP;

  -- Email Notification
  IF p_email IS NOT NULL THEN
    -- Send Mail

    apex_mail.send (p_to        => p_email,
                    p_from      => 'martin@talkapex.com', -- CHANGE THIS!
                    p_body      => 'An unhandled exception happend in an application. Please search logger logs for: ' || v_scope,
                    p_subj      => 'Unhandled Exception in: ' || p_application_id);
  END IF;
END sp_log_error_page;
</pre>
<span style="font-weight:bold;">- Create Error Page</span>
This page will display a user friendly message to the user. For the purposes of this demo Page 200 will be created to handle error messages.

<span style="font-style:italic;">Create Page</span>
Create Page 200

<span style="font-style:italic;">Create a HTML region</span>
Region Name: Unknown Error
Source: An unhandled error occurred. A notification has been sent to the system administrator.

<span style="font-style:italic;">Create a Region Button:</span>
Button Name: Back
Action: Redirect to URL
Execute Validations: No
URL Target: javascript:window.history.go(-1);

<span style="font-style:italic;">Add the following hidden items:</span>
P200_PAGE_ID
P200_ESCAPE
P200_ORA_MSG
P200_APEX_MSG

<span style="font-style:italic;">Create Computation:</span>
Item: P200_ORA_MSG
Point: Before Header
Type: PL/SQL Expression
Computation: REPLACE(:p200_ora_msg,:p200_escape,':');

<span style="font-style:italic;">Create Dynamic Action:</span>
(select Advanced)
Name: Show Error Message Modal
Event: Page Load
Action: Simple Modal - Show
 - Esc Close: No
 - Change Opacity and Background Color as desired
Select Type: Region
 - Region: Unknown Error

Create Page Process:
Type: PL/SQL
Name: Log Error
Point: On Load - Before Header
Source:
<pre class="brush: sql">
DECLARE
BEGIN
  sp_log_error_page (p_scope_prefix => 'apex.demo.', -- Enter what ever you want to help identify your apex errors in the log tables
                     p_application_id => :app_id,
                     p_page_id   => :p200_page_id,
                     p_oracle_err_msg => :p200_ora_msg,
                     p_apex_err_msg => :p200_apex_msg,
                     p_email     => '' -- Enter your email address here
                                      );
END;
</pre>
<span style="font-weight:bold;">- Change Error Template</span>
Go to: Shared Components > Templates
Select the default Page Template <span style="font-style:italic;">(for my demo mine was: One Level Tabs - Right Sidebar (optional / table-based)</span>
Error Page Template:
<pre class="brush: html">
<script type="text/javascript">
  $('body').hide();
  var apexEscape = '*@*'; //Used to replace semi colons in the value so does not invalidate URL
  var oraMsg = $('.ErrorPageMessage').html().replace(/:/g,apexEscape);
  var apexMsg = '#MESSAGE#'.replace(/:/g,apexEscape);
  window.location.href='f?p=&APP_ID.:200:&APP_SESSION.::&DEBUG.:200:P200_PAGE_ID,P200_ESCAPE,P200_ORA_MSG,P200_APEX_MSG:&APP_PAGE_ID.,' + apexEscape + ',\\' + oraMsg + '\\,\\' + apexMsg + '\\'; // Add backslashes to avoid comma issue
</script>
</pre>
<span style="font-weight:bold;">- End Result</span>
When you have an unhandled exception the end users should see a message like:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TI201ohhIrI/AAAAAAAAD0A/gFQ-6Bcu7CI/s400/error_msg_friendly.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TI201ohhIrI/AAAAAAAAD0A/gFQ-6Bcu7CI/s1600/error_msg_friendly.jpg)
You can view all the log information by running the following query:
<pre class="brush: sql">
SELECT *
  FROM logger_logs
 WHERE scope = 'apex.demo.unhandeled_exception{session_id: 652754467566839, guid: 901e0663a0896b35e040007f0100049a}'; -- Replace this scope with the scope that is sent in the email
</pre>
