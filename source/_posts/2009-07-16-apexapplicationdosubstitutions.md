---
title: APEX_APPLICATION.DO_SUBSTITUTIONS
tags:
  - apex
date: 2009-07-16 09:00:00
alias:
---

I've run into several instances where I needed to store HTML in a table. The problem is sometimes the HTML references APEX Items. For example if your HTML needs to reference a picture, odds are you'll need to reference <span style="font-style:italic;">&APP_IMAGES.</span> (or some image location item). In the past I've done manual <span style="font-style:italic;">REPLACE </span>calls for known items, but it was fairly restrictive.

APEX has a great function (not yet documented to my knowledge) called APEX_APPLICATION.DO_SUBSTITUTIONS. If you pass in a string, it will substitute any APEX values. Here's an example app: [http://apex.oracle.com/pls/otn/f?p=20195:2000](http://apex.oracle.com/pls/otn/f?p=20195:2000)

To create demo:

1- Create table and insert values

<pre class="brush: sql">
CREATE TABLE tp2000(line_content CLOB NOT NULL);

INSERT INTO tp2000
     VALUES ('Google Canada Picture: ![]()');

INSERT INTO tp2000
     VALUES ('My Current Session: ' || CHR (38) || 'APP_SESSION.');
</pre>

2- Create Report Region (with substitutions)
<pre class="brush: sql">
SELECT apex_application.do_substitutions (line_content) content_with_subs
  FROM tp2000
</pre>

3- Create Report Region (without substitutions)
<pre class="brush: sql">
SELECT line_content content_without_subs
  FROM tp2000
</pre>
