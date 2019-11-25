---
title: Logger 2.0.0 - Logging Parameters
tags:
  - logger
date: 2013-05-06 08:00:00
alias:
---

_In preparation for the release of [Logger 2.0.0](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility) I've decided to write a few posts highlighting some of the new features._

After using Logger for several years one thing I found that I was doing over and over again is logging the parameters to functions and parameters. The beginning of a procedure usually looked like this:
```sql
procedure my_proc(
  p_empno in number,
  p_hiredate in date,
  p_boolean in boolean) -- not really relevant for proc, but good for demo
as
  l_scope logger_logs.scope%type := 'my_proc';
begin
  logger.log('START', l_scope);
  logger.log('p_empno: ' || to_char(p_empno), l_scope);
  logger.log('p_hiredate: ' || to_char(p_hiredate, 'DD-MON-YYYY HH24:MI:SS'), l_scope);
  logger.log('p_boolean: ' || case when p_boolean then 'TRUE' else 'FALSE' end, l_scope);
  ...
end my_proc;  
```
When you have data types that aren't strings the conversion process can become a bit tedious especially if you have to log a lot of parameters. Of course snippets can help reduce some of the time but it was still annoying.

Another issue that I found was that developers would use their own formatting for logging parameters. Some would include a dash instead of a colon, some would include spaces and others wouldn't, etc. Eventually it became difficult to read the logs with all the different formatting.

To get around these issues Logger now takes in a parameter, `p_params`, which is an array of name/value pairs. Besides visual consistency, it will also handle all the formatting of the different data types so you don't need to spend time on it. The following code snippet is the same as above, but using the new parameters feature:

```sql
procedure my_proc(
  p_empno in number,
  p_hiredate in date,
  p_boolean in boolean) -- not really relevant for proc, but good for demo
as
  l_scope logger_logs.scope%type := 'my_proc';
  l_params logger.tab_param;
begin
  logger.append_param(l_params, 'p_empno', p_empno);
  logger.append_param(l_params, 'p_hiredate', p_hiredate);
  logger.append_param(l_params, 'p_boolean', p_boolean);
  logger.log('START', l_scope, null, l_params);
  ...
end my_proc;  
```

If you now query `logger_logs` you'll now see the parameters appended to the `extra` column (note if you defined something in the `p_extra` parameter it would still appear):

```sql
select text, scope, extra
from logger_logs_5_min;

TEXT  SCOPE     EXTRA
----- --------- ----------------------------------
START my_proc   *** Parameters ***
                p_empno: 123
                p_hiredate: 26-APR-2013 03:14:58
                p_boolean: TRUE
```
You'll notice that all the formatting and implicit conversions have been taken care of.

**Performance**

Unless you are running Logger in [no-op mode](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#no-op-option-for-production-environments) then there will be a slight performance hit each time you append a parameter (for the cost of appending to an array and implicit conversion). You'll need to factor this in when leveraging this feature. Odds are you'll be able to use the `p_params` parameter on most of your procedures and functions, but not on frequently used methods.

**Other Considerations**

Not all people will use the parameters option, and that's fine. We made it an optional parameter specifically for that reason. We added this feature to support one of the primary goals of Logger which is "to be as simple as possible to use".
