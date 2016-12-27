---
title: "Application ID Reserved" - Fix
date: 2009-02-05 14:10:00
tags: apex
alias:
---

When you upgrade an application do you ever get the following `Application ID Reserved` error message?

![](http://3.bp.blogspot.com/_33EF80fk9sM/SYtWlmBrUOI/AAAAAAAADl4/MNxQkrJD8LA/s400/application_id_reserved.jpg)

Even if you delete your application and try to re-import using the same application ID you'll get the message.

To view "reserved" application ids you can run the following query (please note you may need to be a dba user):

```sql
select *
from flows_030100.wwv_flows_reserved;
```

In order to fix this problem you just need to delete the application id from that table:

```sql
delete
  from flows_030100.wwv_flows_reserved
  where id = :p_app_id; -- Your application ID that is locked
```
