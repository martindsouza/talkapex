---
title: Logger with Batch Processes
tags:
  - logger
date: 2013-09-13 09:01:00
alias:
---

At [ODTUG](http://odtug.com/) [APEXposed](http://www.odtug.com/apexposed) this week [Logger](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility) which highlighted some of its features. The item that got the most interest from participants was the ability to set the logging level for individual sessions without affecting any other sessions. This wasn't a big surprise since this was the key feature that most people had asked to see in the 2.0.0 release.

One attendee asked if we could modify the logging level in a particular session from another session while it was still running. The answer is yes you can. The best example I could give at the time was baking a loaf of bread. Most ovens have a window and a light. Half way during the baking process you can turn on the light and see how your bread is doing. Logger allows you to do that as well.  The following example highlights this:

```sql
SQL> -- Simulate production setup
SQL> exec logger.set_level('ERROR');

PL/SQL procedure successfully completed.

SQL> -- Procedure to simulate batch script

create or replace procedure run_long_batch(
  p_client_id in varchar2,
  p_iterations in pls_integer)
as
  l_scope logger_logs.scope%type := 'run_long_batch';
begin
  logger.log('START', l_scope);

  dbms_session.set_identifier(p_client_id);

  for i in 1..p_iterations loop
    logger.log('i: ' || i, l_scope);
    dbms_lock.sleep(1);
  end loop;

  logger.log('END');
 17  
end run_long_batch;
 19  /

Procedure created.

SQL> -- *** In another session run the following code ***
SQL> exec run_long_batch(p_client_id => 'batchdemo', p_iterations => 60);

SQL> -- In current window toggle the logging level for the batch job
SQL> -- Note: This is done while it's still running in the other session
begin

  -- Enable debug mode for batch job
  logger.set_level('DEBUG', 'batchdemo');
  dbms_lock.sleep(2);
  -- Disable
  logger.unset_client_level('batchdemo');
  dbms_lock.sleep(10);
  -- Enable again
  logger.set_level('DEBUG', 'batchdemo');
  dbms_lock.sleep(2);
  -- Disable
  logger.unset_client_level('batchdemo');
end;
 15  /

PL/SQL procedure successfully completed.

SQL> -- View items that were logged
select logger_level, text, time_stamp, scope
from logger_logs
order by id;

LOGGER_LEVEL TEXT       TIME_STAMP
------------ ---------- ---------------------------------------------------------------------------
          16 i: 8       11-SEP-13 01.40.34.879699 PM
          16 i: 9       11-SEP-13 01.40.35.881973 PM
          16 i: 20      11-SEP-13 01.40.46.890244 PM
          16 i: 21      11-SEP-13 01.40.47.891464 PM

```

From the query's output you'll notice that it started logging, then stopped for a short period, then started again, then stopped. This can really help if a batch process is taking a long time and you want to see where exactly in the code it is.
