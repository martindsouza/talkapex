---
title: SQLcl and login.sql
tags:
  - sqlcl
  - oracle
date: 2015-05-18 08:59:00
alias:
---

In SQL*Plus you can configure it to automatically run login scripts, typically used to configure your session. These are stored in the files `glogin.sql` and `login.sql`. Some examples that you'd find in these files are statements like: `set serveroutput on`.  You can read more about login scripts [here](http://www.adp-gmbh.ch/ora/sqlplus/login.html).

SQLcl also handles login scripts, however it may require some additional configuration. The easiest way to leverage this is to create a file called `login.sql` in your current directory, then call SQLcl from that directory.

If you're like me and launch SQLcl from many directories that approach won't work. Instead, you can create one `login.sql` file and have it automatically referenced. To do this create `login.sql` in a folder (in my case it was `Documents/Oracle`) and then add the following to `~/.bashprofile`:  `export SQLPATH=~/Documents/Oracle/`. _For those that use [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) modify `~/.zshrc` instead._

Now when you run SQLcl anywhere it will reference your `login.sql` file.

_Thanks to [Kris Rice](https://twitter.com/krisrice) and [Jeff Smith](https://twitter.com/thatjeffsmith) for helping with this._
