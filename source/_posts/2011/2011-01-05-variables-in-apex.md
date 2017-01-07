---
title: Variables in APEX
tags:
  - apex
date: 2011-01-05 08:00:00
alias:
---

When I first started using APEX I was unsure about which method to use when referencing variables (values in session state). The purpose of this post is to outline the different ways to reference APEX variables and provide an examples for each case.

They're five ways to reference variables in APEX:

*   :Bind_variables
*   &amp;Substitution_strings.
*   V / NV functions
*   #Hash#
*   "Shortcuts"<span style="font-weight: bold;">Bind Variables</span>
Bind variables can be used in any block of SQL or PL/SQL code inside APEX. For example if creating a report to display all the employees in a selected department the query would be:
<pre class="brush: sql;highlight:3">SELECT ename
  FROM emp
 WHERE deptno = :p1_deptno</pre>Where P1_DEPTNO is a page item that you created.

<span style="font-weight: bold;">Substitution Strings</span>
Substitution strings use the <span style="font-style: italic;">&amp;variable.</span> notation. They can be used anywhere in your APEX application such as a HTML region or even a template. You can also use them in inline SQL and PL/SQL code but it is not recommended for security reasons. <span style="font-style: italic;">For more information on this security risk read the following Oracle white paper on [SQL Injection](http://www.oracle.com/technetwork/database/features/plsql/overview/how-to-write-injection-proof-plsql-1-129572.pdf)</span>

A simple example of using a substitution string is in a HTML region which displays a welcome message. The region source would be:
<pre class="brush: html;highlight:1">Hello &amp;APP_USER.

Welcome to the demo application.</pre><span style="font-weight: bold;">V / NV Functions</span>
If you want to reference APEX variables in compiled code, such as views, packages, and procedures, you can't use bind variables. The easiest way to reference the variables is to use the V (for strings) and NV (for numbers) functions. For example:
<pre class="brush: sql;highlight:5">CREATE OR REPLACE PROCEDURE log_apex_info
AS
BEGIN
  INSERT INTO tapex_log ( username, apex_page_id, access_date)
       VALUES (V ('APP_USER'), NV ('APP_PAGE_ID'), SYSDATE);
END;</pre><span style="font-weight: bold;">#Hash#</span>
The hash notation of <span style="font-style: italic;">#variable#</span> is used in multiple places. When creating column links in a report you can use the hash notation to represent column values. The following figure highlights how column values are referenced in a report link. <span style="font-style: italic;">#EMPNO#</span> is used to reference the EMPNO column and <span style="font-style: italic;">#JOB#</span> is used to pass the job as a parameter to Page 2.

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TR6GfgyIN0I/AAAAAAAAD20/TqVsO0rXg7g/s400/hash_column_link.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TR6GfgyIN0I/AAAAAAAAD20/TqVsO0rXg7g/s1600/hash_column_link.jpg)
The hash notation is also used in templates (Shared Components &gt; Templates). When modifying a template they're special variables available depending on the type of template. These variables can be found at the bottom of the screen in the Substitution Strings section.

<span style="font-weight: bold;">Shortcuts</span>
Shortcuts can only be used in specific areas within APEX (such as some regions, labels, and templates) and can be rendered both statically or dynamically (using PL/SQL functions). To reference a Shortcut use the "shortcutname" notation (quotes included). The following article covers Shortcuts in more detail, including where you can use them in APEX: [http://www.talkapex.com/2014/02/apex-shortcuts.html](http://www.talkapex.com/2014/02/apex-shortcuts.html)

For more information about referencing variables in APEX read the [Referencing Session State](http://download.oracle.com/docs/cd/E17556_01/doc/user.40/e15517/concept.htm#BEICHBBG) section in the builder guide.
