---
title: How to Share SQLcl Aliases
tags:
  - sqlcl
  - sql
  - plsql
date: 2020-03-11 03:46:00
alias:
---


[SQLcl](https://www.oracle.com/ca-en/database/technologies/appdev/sqlcl.html) supports [aliases](https://docs.oracle.com/en/database/oracle/sql-developer-command-line/19.2/sqcug/working-sqlcl.html#GUID-0EF19838-E7C0-481B-8B09-202DF8277C65) which can save a lot of time when running the same queries or scripts by not having to type them each time. A simple example of an aliases is to show the current date. Instead of having to type `select sysdate from dual;` each time you could do this:

```sql
-- Only do this once
alias today=
  select sysdate
  from dual
;

-- To run it just type: today

SQL> today
     SYSDATE
____________
11-MAR-20
```

*If you are new to SQLcl aliases or the concept of `login.sql` (which runs each time SQLcl is launched) I suggest reading the following articles before continuing with this article.*

- *[Intro to aliases by Jeff Smith](https://www.thatjeffsmith.com/archive/2015/11/object-search-in-sqlcl/)*
- {% post_link sqlcl-and-loginsql login.sql %}


If you use multiple machines or want to share aliases you may think you're limited to sending code snippets to people or blogging about them. Luckily SQLcl can reference files stored on the web! I've posted some of my SQLcl aliases on Github [here](https://github.com/martindsouza/oracle-sqlcl). To load them just run [`@https://raw.githubusercontent.com/martindsouza/oracle-sqlcl/master/aliases.sql`](https://raw.githubusercontent.com/martindsouza/oracle-sqlcl/master/aliases.sql) in SQLcl. If you want to constantly get an updated version of these aliases just add the line above to your `login.sql` file. I'm currently working on fine tuning my SQLcl aliases and will keep Github file up to date.

One example of a alias I have is called `invalid` which lists all invalid objects in your schema. Example:

```sql
SQL> invalid
                OBJECT_NAME     OBJECT_TYPE
___________________________ _______________
BIU_OOW_DEMO_EVENT_LOG      TRIGGER
BI_OOW_DEMO_HIST_GEN_LOG    TRIGGER
EBA_DEMO_IG_TEXT_PKG        PACKAGE BODY
OOW_DEMO_GEN_DATA_PKG       PACKAGE BODY
OOW_DEMO_PREFERENCES_BIU    TRIGGER
PKG_ORDS                    PACKAGE BODY
PKG_ORDS                    PACKAGE
```

Some closing thoughts:

- If referencing other people's aliases (via the web / `.sql` file) be careful to either review the file or ensure it comes from a trusted source as malicious code could be easily added.
- Instead of having a lot of content in your `login.sql` file you can keep it to one line (referencing a file on Github for example) and keep the Github file updated with everything. This way if you change machines or use multiple machines they'll all have the same experience.



