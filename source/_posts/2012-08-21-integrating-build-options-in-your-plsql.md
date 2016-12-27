---
title: Integrating Build Options in your PL/SQL Code
tags:
  - apex
  - plsql
date: 2012-08-21 07:00:00
alias:
---

Build Options is a great tool to enable or disable certain objects in APEX. If you've never used them before or don't know what they are I suggest you read [Scott Wesley's article](http://www.grassroots-oracle.com/2010/06/oracle-apex-build-options.html).

I typically use Build Options to display data for developers that should not be displayed in production. This can help improve development time since you don't have at include, then remove, code in an application before it is migrated to production.

Build Options are great for APEX but what about PL/SQL code that is directly associated with an APEX application? You could enable and disable certain page processes with build options but that doesn't allow "fine grained Build Option" control in your PL/SQL code.

Thankfully you can view your Build Options, and their status, with an [APEX Dictionary](http://www.talkapex.com/2008/11/how-to-list-apex-dictionary-views-using.html) view:&nbsp; APEX_APPLICATION_BUILD_OPTIONS.

Here is a function that I wrote that will allow you to easily see if a given Build Option is enabled in your PL/SQL code:
<pre class="brush: sql;">/**
 * Returns Y or N if a build option is enabled
 *
 * @param p_build_option_name Name of build option (case sensitive)
 *  - You can change this to not be case sensitive if applicable
 * @param p_app_id Application ID, default current Application ID
 *  - Included this as a parameter in case testing from straight PL/SQL
 * @return Y or N
 * @author Martin Giffy DSouza http://www.talkapex.com
 * @created 15-Aug-2012
 */
create or replace FUNCTION f_is_build_option_enabled(
  p_build_option_name IN apex_application_build_options.build_option_name%TYPE,
  p_app_id IN apex_application_build_options.application_id%TYPE DEFAULT nv('APP_ID'))
  return varchar2
AS
  l_build_option_status apex_application_build_options.build_option_status%type;
BEGIN

  SELECT upper(build_option_status)
  into l_build_option_status
  FROM apex_application_build_options
  WHERE application_id = p_app_id
    AND build_option_name = p_build_option_name;

  IF l_build_option_status = 'INCLUDE' THEN
    RETURN 'Y';
  ELSE
    RETURN 'N';
  END IF;
exception
  WHEN others THEN
    -- Your call on how to handle errors
    raise;
END</pre>Here's an example of how you can use it in your PL/SQL code. Note that it doesn't pass in the application id since it is assuming that you're executing the PL/SQL as part of the APEX application:
<pre class="brush: sql;">...
IF f_is_build_option_enabled(p_build_option_name => 'DEV_ONLY') = 'Y' THEN
  htp.p('DEV_ONLY build option enabled');
ELSE
  htp.p('DEV_ONLY build option disabled');
END IF;
...</pre>
