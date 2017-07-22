---
title: Logger 2.1.0 - ok_to_log
tags:
  - logger
date: 2013-08-07 07:00:00
alias:
---

_In preparation for the release of Logger 2.1.0 I've decided to write a few posts highlighting some of the new features._

A while ago, someone asked me to add a feature into logger to determine if a log statement would actually be logged. I fought this off like the plague as I wanted to <u>avoid</u> the following situation:

```sql
...
if can_log then
  logger.log('some message');
end if;
...

if can_log then
  logger.log('another message');
end if;
...
```

Unfortunately I've seen this type of coding style many times and it makes code difficult to read and maintain. This also ruins one of the key goals behind Logger which is to make it easy and quick for developers to use.

Reluctantly, I finally caved in. As a result there's a new function called [`ok_to_log`](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Logger-API#wiki-procedure-ok_to_log). `ok_to_log` tells you if what you're about to log will actually get logged. **It is <u>not</u> meant to be used like the example above**. I can't emphasize this enough.

The main use case for `ok_to_log` is for situations where you <u>only</u> want to do something for logging purposes that will slow down the system. A good example is when you want to log the values of an array. Since it will take time to loop through the array there's no point in doing it if Logger won't actually log anything. The following example highlights this:

```sql
select *
declare
  type typ_array is table of number index by pls_integer;
  l_array typ_array;
begin
  -- Load test data
  for x in 1..100 loop
    l_array(x) := x;
  end loop;

  -- Only log if logging is enabled
  if logger.ok_to_log('DEBUG') then
    for x in 1..l_array.count loop
      logger.log(l_array(x));
    end loop;
  end if;
end;
/
```

If the logging level is set to DEBUG or higher (as it would be in development environments) then logger will loop through the array and log the values. If the logging level is set to `ERROR` (as it normally would be in production instances) it won't loop over the array since it now knows ahead of time that Logger won't log the values.

Note that the level passed into `ok_to_log` should correspond to the logging statements used inside the block of code that you want to log. If they don't, it really won't make sense and could result in unnecessary performance hits.

To summarize the `ok_to_log` function:
- I was reluctant to add it in for obvious reasons, please don't make me regret it.
- Only use in situations where you need to perform an expense block of code only for logging purposes.
- Make sure the level passed in `ok_to_log` matches the log statements using inside the associated block of code.
