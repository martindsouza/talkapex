---
title: 'Oracle XE and APEX: Where is my Trace File?'
tags:
  - apex
  - oracle
date: 2010-10-20 09:00:00
alias:
---

In APEX you trace your session by adding <span style="font-style:italic;">&p_trace=YES</span> at the end of your URL. For example: http://fcapex40:8080/apex/f?p=101:1:2603384364125271::NO&p_trace=YES Please refer to the [APEX documentation](http://download.oracle.com/docs/cd/E17556_01/doc/user.40/e15517/debug.htm#BABGDGEH) for more information regarding traces in APEX.

The documentation states that the trace file is located in the following directory:
<pre class="brush: sql; highlight: 2">
SQL> -- Run as SYSTEM
SQL>  SHOW PARAMETERS user_dump_dest

NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
user_dump_dest                       string      /usr/lib/oracle/xe/app/oracle/
                                                 admin/XE/udump
</pre>
This isn't the case if you're using the Embedded PL/SQL Gateway, which is how APEX is run on Oracle XE. Thanks to Jari for answering my question on the APEX forum about this: [http://forums.oracle.com/forums/thread.jspa?forumID=137&threadID=1771547](http://forums.oracle.com/forums/thread.jspa?forumID=137&threadID=1771547)

For APEX installations that use the PL/SQL Embedded Gateway (i.e. Oracle XE) the trace file is located in the following directory:
<pre class="brush: sql: highlight: 1">
SQL> SHOW PARAMETER background_dump_dest

NAME                                 TYPE        VALUE
------------------------------------ ----------- ------------------------------
background_dump_dest                 string      /usr/lib/oracle/xe/app/oracle/
                                                 admin/XE/bdump
</pre>
To find the trace file that was created for your session:
<pre class="brush: shell;">
$ cd /usr/lib/oracle/xe/app/oracle/admin/XE/bdump
$ grep -l "APP_SESSION" *.trc
#example
$ grep -l "2603384364125271" *.trc
</pre>
