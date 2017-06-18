---
title: Logger 2.0.0 - Session Specific Logging - Advanced Features
tags:
  - logger
date: 2013-05-08 07:30:00
alias:
---

_In preparation for the release of [Logger 2.0.0 ](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility) I've decided to write a few posts highlighting some of the new features._

In my [previous post](http://www.talkapex.com/2013/05/logger-200-enable-session-specific.html) I covered how to set and unset session specific logging based on the Client Identifier. In this post I'll cover some advanced options for this functionality. Before continuing, please be sure to read the previous post or else this won't make sense.

In the previous post the demo code to set a session specific logging level only used two parameters: the logging level and the Client Identifier. In `logger.set_level` they're actually four parameters:

**`p_level`**: This is the level to set. If p_client_id is null then it will set the system level configuration in `logger_prefs` > `LEVEL`.

The additional parameters are only valid if `p_client_id` is defined.

**`p_client_id`**: If not null, will apply `p_level` for the specific client_id only. If null then `p_level` will be applied for the system level configuration.

**`p_include_call_stack`**: Determines if the call stack should be stored. If null then will use the default setting in `logger_prefs` > `INCLUDE_CALL_STACK`. Valid values are `TRUE` and `FALSE` (strings).

**`p_client_id_expire_hours`**: Will end session specific logging after set hours. If not defined then the default setting will be used` logger_prefs` > `PREF_BY_CLIENT_ID_EXPIRE_HOURS`. Note that this will not be exact as a job is run hourly to clean up expired session specific logging that has expired for a given client_id. Instead of waiting for the job to clean up expired sessions you can explicitly [unset](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#unset-client-specific-logging) them.

## How to View All Client_id Settings

To view the system level settings you can use the [`logger.status`](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility#status) procedure. There was debate as to whether or not we include all the client_id settings in this procedure. In the end we did not include it list them in this procedure as the list may get very long. To view all the session specific logging configurations use the following query:

```sql
select *
from logger_prefs_by_client_id;
```

## Renewing

When calling `logger.set_level` with a `client_id `that already exists in `logger_prefs_by_client_id` it will update the values and update the expiry date (i.e. you extend the setting).
