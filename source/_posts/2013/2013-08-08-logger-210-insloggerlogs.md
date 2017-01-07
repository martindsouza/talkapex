---
title: Logger 2.1.0 - ins_logger_logs
tags:
  - logger
date: 2013-08-08 07:00:00
alias:
---

_In preparation for the release of Logger 2.1.0 I've decided to write a few posts highlighting some of the new features._ 

Prior to 2.1.0, Logger used a trigger when inserting into the <span style="font-family: inherit;">LOGGER_LOGS</span> table. Though their was never a compelling reason to remove the trigger a [bug request](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/issues/26) and an unrelated [enhancement request](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/issues/17) prompted me to reexamine the need for it.

Starting with 2.1.0 there is no trigger on the primary Logger table: <span style="font-family: inherit;">LOGGER_LOGS</span>. Instead all insert statements are handled by a single procedure:&nbsp; [<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">ins_logger_logs</span>](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Logger-API#wiki-procedure-ins_logger_logs). I'm sure this will make [Tom Kyte](https://twitter.com/OracleAskTom) happy as there's one less trigger in the world now.

On the performance perspective, initial testing has shown a ~15% performance increase when using the standard logging procedures. Though this may not seem like a lot, in large systems where Logger is called often it can start to add up. 

As you can imagine, if you have a direct insert into <span style="font-family: inherit;">LOGGER_LOGS</span> and leveraged some of the features that the trigger had (i.e. obtaining the sequence for the ID column) then your application will have a run time error. I was very hesitant about removing the trigger for this sole reason but am able to justify it by the fact that directly inserting into the table has never been supported by Logger. Regardless, if you do have insert statements into the table you can simply replace them with calls to the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">ins_logger_logs</span> procedure.

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">ins_logger_logs</span> should not be used often and only makes sense in very specific cases. A few things to note about this procedure:
- It is an [autonomous transaction](http://www.oracle-base.com/articles/misc/autonomous-transactions.php) procedure. This means that an explicit commit occurs at the end of the procedure. Since it is an autonomous transaction it will not affect (i.e. not commit) your current session.
- It will always insert the value into the <span style="font-family: inherit;">LOGGER_LOGS t</span>able. No check is performed to factor in the current logging level. For this reason you're better off using the standard Logger procedures.

On a side note, you may be wondering why you would manually do a direct insert into the <span style="font-family: inherit;">LOGGER_LOGS</span> table? In rare occasions you may need the ID that was generated from a log statement for other purposes. None of primary log procedures returns the ID. One situation that I've seen in the past is to log all the APEX info on the APEX error handling page. On that page the user would see an error code that developers can reference. That unique code is actually the ID from Logger.

Here is an example of the new procedure:
<pre class="brush: sql;">set serveroutput on

declare
  l_id logger_logs.id%type;
begin
  -- Note: Commented out parameters not used for this demo (but still accessible via API)
  logger.ins_logger_logs(
    p_logger_level =&gt; logger.g_debug,
    p_text =&gt; 'Custom Insert',
    p_scope =&gt; 'demo.logger.custom_insert',
--    p_call_stack =&gt; ''
    p_unit_name =&gt; 'Dynamic PL/SQL',
--    p_line_no =&gt; ,
--    p_extra =&gt; ,
    po_id =&gt; l_id
  );

  dbms_output.put_line('ID: ' || l_id);
end;
/

ID: 2930650
</pre>