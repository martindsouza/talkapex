---
title: Logger 2.0.0 - Session Specific Logging - Advanced Features
tags:
  - logger
date: 2013-05-08 07:30:00
alias:
---

_In preparation for the release of [Logger 2.0.0 ](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility) I've decided to write a few posts highlighting some of the new features.&nbsp;_

In my [previous post](http://www.talkapex.com/2013/05/logger-200-enable-session-specific.html) I covered how to set and unset session specific logging based on the Client Identifier. In this post I'll cover some advanced options for this functionality. Before continuing, please be sure to read the previous post or else this won't make sense.

In the previous post the demo code to set a session specific logging level only used two parameters: the logging level and the Client Identifier. In <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger.set_level</span> they're actually four parameters:

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">**p_level**</span>

This is the level to set. If p_client_id is null then it will set the system level configuration in <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger_prefs</span> &gt; <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">LEVEL</span>.

_The additional parameters are only valid if <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">p_client_id</span> is defined._

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">**p_client_id**</span>

If not null, will apply <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">p_level</span> for the specific client_id only. If null then <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">p_level</span> will be applied for the system level configuration.

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">**p_include_call_stack**</span>

Determines if the call stack should be stored. If null then will use the default setting in <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger_prefs</span> &gt; <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">INCLUDE_CALL_STACK</span>. Valid values are TRUE and FALSE (strings).

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">**p_client_id_expire_hours**</span>

Will end session specific logging after set hours. If not defined then the default setting will be used<span style="font-family: &quot;Courier New&quot;,Courier,monospace;"> logger_prefs</span> &gt; <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">PREF_BY_CLIENT_ID_EXPIRE_HOURS</span>. Note that this will not be exact as a job is run hourly to clean up expired session specific logging that has expired for a given client_id. Instead of waiting for the job to clean up expired sessions you can explicitly [unset](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#unset-client-specific-logging) them.

**How to View All Client_id Settings**

To view the system level settings you can use the [<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger.status</span>](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#status) procedure. There was debate as to whether or not we include all the client_id settings in this procedure. In the end we did not include it list them in this procedure as the list may get very long. To view all the session specific logging configurations use the following query: <pre class="brush: sql;">
select *
from logger_prefs_by_client_id;
</pre>
**Renewing**

When calling <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger.set_level</span> with a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">client_id </span>that already exists in <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">logger_prefs_by_client_id</span> it will update the values and update the expiry date (i.e. you extend the setting).