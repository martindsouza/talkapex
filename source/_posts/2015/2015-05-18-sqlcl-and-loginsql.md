---
title: SQLcl and login.sql
tags:
  - sqlcl
  - oracle
date: 2015-05-18 08:59:00
alias:
---

In SQL*Plus you can configure it to automatically run login scripts, typically used to configure your session. These are stored in the files <span style="font-family: Courier New, Courier, monospace;">glogin.sql</span> and <span style="font-family: Courier New, Courier, monospace;">login.sql</span>. Some examples that you'd find in these files are statements like:&nbsp;<span style="font-family: Courier New, Courier, monospace;">set serveroutput on</span>. &nbsp;You can read more about login scripts [here](http://www.adp-gmbh.ch/ora/sqlplus/login.html).

SQLcl also handles login scripts, however it may require some additional configuration. The easiest way to leverage this is to create a file called <span style="font-family: Courier New, Courier, monospace;">login.sql</span> in your current directory, then call SQLcl from that directory.

If you're like me and launch SQLcl from many directories that approach won't work. Instead, you can create one <span style="font-family: Courier New, Courier, monospace;">login.sql</span> file and have it automatically referenced. To do this create <span style="font-family: Courier New, Courier, monospace;">login.sql</span> in a folder (in my case it was <span style="font-family: Courier New, Courier, monospace;">~/Documents/Oracle</span>) and then add the following to <span style="font-family: Courier New, Courier, monospace;">~/.bash_profile</span>: &nbsp;&nbsp;<span style="font-family: Courier New, Courier, monospace;">export SQLPATH=~/Documents/Oracle/</span>
<span style="font-family: Courier New, Courier, monospace;">
</span><span style="font-family: inherit;">Now when you run SQLcl anywhere it will reference your </span><span style="font-family: Courier New, Courier, monospace;">login.sql</span><span style="font-family: inherit;"> file.</span>
<span style="font-family: Courier New, Courier, monospace;">
</span>_<span style="font-family: inherit;">Thanks to [Kris Rice](https://twitter.com/krisrice) and [Jeff Smith](https://twitter.com/thatjeffsmith) for helping with this.</span>_
