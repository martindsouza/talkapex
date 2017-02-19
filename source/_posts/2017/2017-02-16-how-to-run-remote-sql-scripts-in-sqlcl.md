---
title: How to Run Remote SQL Scripts in SQLcl
tags:
  - sqlcl
  - plsql
  - sql
date: 2017-02-16 18:29:09
alias:
---


Over the past few years of developing open source PL/SQL tools I've learned that one of the key things to make a project successful is have a frictionless install process. Node.js does this very well with their Node Package Manager (`npm`). Currently there is no largely adopted solution like `npm` for Oracle tools so one must usually provide a downloadable zip file containing the code along with installation instructions.

Recently I found out that [SQLcl](http://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html) (and I think [SQL*Plus 12.1](http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html)) support running remote SQL scripts. The following is an example of running a remote script from SQLcl:

```sql
SQL> @https://gist.githubusercontent.com/martindsouza/91742f28c39ed2d41d35d80b6c4cc4c1/raw/d0f28ab7e8af5f64629ad480bad570764cece543/test.sql
hello from github
SQL>
```

The above script is a very simple one: `prompt hello from github` which can be adapted to contain a full install process.

The nice thing about running remote SQL scripts is that no ACL settings are required on the server nor SSL certificates since the client (i.e. your machine) is making the web request. The bad thing about this is that it opens the door for a lot of vulnerability potentials. **It's recommended to always review the script before running it.**
