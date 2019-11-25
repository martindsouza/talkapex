---
title: Exporting APEX Application in SQLcl with Build Status Override
tags:
  - apex
  - sqlcl
date: 2018-07-28 08:09:02
alias:
---



[SQLcl](https://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html) has a great feature to easily export an APEX application.

```bash
> sqlcl giffy/giffy@localhost:32122/orclpdb1810.localdomain

SQLcl: Release 18.2 Production on Sat Jul 28 08:12:13 2018

Copyright (c) 1982, 2018, Oracle.  All rights reserved.

Last Successful login time: Sat Jul 28 2018 08:12:20 -04:00

Connected to:
Oracle Database 12c Enterprise Edition Release 12.2.0.1.0 - 64bit Production

SQL> apex export 100
Exporting application 100  Completed at Sat Jul 28 08:13:14 EDT 2018

SQL>
```

This will produce a file in the current directory called `f100.sql`.

I was recently asked how to export an application using SQLcl and set the `Build Status Override` option. This is available when manually exporting the application as shown below. This option is usually set to `Run Application Only` in production environments to prevent others from modifying the application.

{% asset_img Snip20180728_32.png %}

There is no option _(that I know of)_ to set the `Build Status Override` option when doing an SQLcl export. After doing a diff between manual exports with the different settings for `Build Status Override` the only change is the parameter `p_build_status=> 'RUN_ONLY'`:

{% asset_img Snip20180728_28.png %}

If you want to create an application export for a run only environment (i.e. production) just run the following bash commands after the export:

```bash
APEX_APP_ID=100
sed -i.bu "s/wwv_flow_api.create_flow(/wwv_flow_api.create_flow(p_build_status=> 'RUN_ONLY',/g" f$APEX_APP_ID.sql

mv f"$APEX_APP_ID".sql.bu f"$APEX_APP_ID"_run_and_build.sql
mv f"$APEX_APP_ID".sql f"$APEX_APP_ID"_run_only.sql
```

This will produce two files:

```bash
> ls -1 f*.sql

f100_run_and_build.sql
f100_run_only.sql
```