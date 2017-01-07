---
title: Logger 2.0.0 - Enable Session Specific Logging
tags:
  - logger
date: 2013-05-07 07:30:00
alias:
---

_In preparation for the release of [Logger 2.0.0 ](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility)I've decided to write a few posts highlighting some of the new features._

One of the most common feature request for Logger has been the ability to enable logging (or setting the logger level) for a given session. This post covers how to set and unset session specific logging.

**Logger Levels**

There is no "on/off" switch for Logger. Instead, like most other logging and code instrumentation tools, it supports [multiple levels](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#logger-levels). This allows developers to turn up and down the amount of logging. In most development systems the logging level is set to debug mode and in most production instances it's set to error mode. This means that in production only items that are explicitly logged using <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger.log_error</span> will be stored. For more information read the [configuration](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#configuration) section of the documentation.

**Setting the Logger Level**

Prior to Logger 2.0.0 you could only configure the logging level for the entire schema. To following code snippet highlights this: <pre class="brush: sql;">
exec logger.set_level('DEBUG');

begin
  -- All of these will be logged
  logger.log('Logging log level');
  logger.log_information('Logging information level');
  logger.log_warning('Logging warning level');
  logger.log_error('Logging error level');
end;
/

exec logger.set_level('WARNING');

begin
  logger.log('Logging log level'); -- Not stored
  logger.log_information('Logging information level'); -- Not stored
  logger.log_warning('Logging warning level'); -- Stored
  logger.log_error('Logging error level'); -- Stored
end;
/
</pre>What happens in production systems when a user is experiencing some issues and you want to see all their logging information? The only way to do this before was to set the logging level to debug mode and the entire schema would be affected. This could slow down all the applications in the schema which is an undesired affect. 

**Setting by Session**
**&nbsp;** 
The term "session" is a bit misleading. Since more most Oracle applications are stateless it's not realistic to enable logging for a specific Oracle session (SID). Instead Logger uses the client_identifier (also referred to as client_id). Client identifiers can easily be configured and are commonly used in stateless applications. For example, APEX sets the client_id with: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">:APP_USER || ':' || :APP_SESSION</span>

The following example demonstrates how to enable "session" specific logging in Logger: <pre class="brush: sql;">
-- Connection 1
exec logger.set_level ('ERROR');

begin
  logger.log('will not get stored');
  logger.log_error('will get stored');
end;
/

select id, logger_level, text, client_identifier
from logger_logs_5_min;

  ID LOGGER_LEVEL TEXT                 CLIENT_IDENTIFIER
---- ------------ -------------------- --------------------
  13            2 will get stored      

-- Connection 2 (i.e a different connection)
exec dbms_session.set_identifier('logger_demo');

exec logger.set_level('DEBUG', sys_context('userenv','client_identifier'));
-- Or could have used: logger.set_level('DEBUG', 'logger_demo');

begin
  logger.log('will get stored');
  logger.log_error('will get stored');
end;
/

select id, logger_level, text, client_identifier
from logger_logs_5_min;

  ID LOGGER_LEVEL TEXT                 CLIENT_IDENTIFIER
---- ------------ -------------------- --------------------
  14           16 will get stored      logger_demo
  15            2 will get stored      logger_demo
</pre>**Unsettting by Session**

When setting a session specific logging level, Logger will apply an expiration time to it. By default&nbsp; this is set to 12 hours but can be configured by setting the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger_prefs</span> value:&nbsp; <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">PREF_BY_CLIENT_ID_EXPIRE_HOURS</span>&nbsp; You can also pass in the time using the parameter <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">p_client_id_expire_hours</span>. If you don't explicitly unset a specific session then logger will automatically clean up these sessions after the desired time.

They're three ways to explicitly unset session specific logging: <pre class="brush: sql;">
-- Unset by specific client_id
exec logger.unset_client_level(p_client_id => 'logger_demo');

-- Unset all sessions that have expired
exec logger.unset_client_level;

-- Unset all sessions (regardless of expiration time)
exec logger.unset_client_level_all
</pre>**Summary**

By setting session specific logging you can allow for an individual connection to have logging enabled without affecting the rest of the applications in the schema.
** **
They're some other interesting features in session specific logging that I wasn't able to fit this article. In the [next article](http://www.talkapex.com/2013/05/logger-200-session-specific-logging.html) I'll discuss these features.
**&nbsp;**