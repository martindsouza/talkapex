---
title: APEX Spring Cleaning
tags:
  - apex
date: 2010-08-31 08:55:00
alias:
---

Each time you do a major upgrade APEX creates a new schema. It does not remove the older schemas, which allows you to roll back to previous versions in case something happens.

I was upgrading some older instances of APEX and realized that I still had some of these older schemas lying around and decide that it was time to do some spring cleaning <span style="font-style:italic;">(I realize that isn't exactly spring time, unless you live in Australia)</span>.

From the APEX installation guide ([http://download.oracle.com/docs/cd/E17556_01/doc/install.40/e15513/otn_install.htm#CBHBABCC](http://download.oracle.com/docs/cd/E17556_01/doc/install.40/e15513/otn_install.htm#CBHBABCC)) here's how to identify and remove older versions of APEX.

<span style="font-style:italic;color:red;">Please read the documentation and understand what exactly you're doing before you do this!</span>

<span style="font-weight:bold;">- Identify old APEX schemas</span>
<pre class="brush: sql">
SELECT username
   FROM dba_users
 WHERE (username LIKE 'FLOWS_%' OR USERNAME LIKE 'APEX_%')
   AND USERNAME NOT IN (
        SELECT 'FLOWS_FILES'
          FROM DUAL
         UNION
        SELECT 'APEX_PUBLIC_USER' FROM DUAL
         UNION
        SELECT SCHEMA s
           FROM dba_registry
         WHERE comp_id = 'APEX')
</pre>
<span style="font-weight:bold;">- Remove old schemas</span>
Connect as SYS using the SYSDBA role then run:
<pre class="brush: sql">
DROP USER FLOWS_030000 CASCADE; -- Where "FLOWS_030000" is the username from the previous query
</pre>
