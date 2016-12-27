---
title: How to Find Current APEX Version
tags:
  - APEX
  - ORACLE APEX
date: 2010-06-18 09:00:00
alias:
---

Last week my DBA asked me what version of APEX we were running. Instinctively I loaded up the APEX development page and looked in the bottom right hand corner:

[![](http://2.bp.blogspot.com/_33EF80fk9sM/TBbeBbiAPWI/AAAAAAAADxI/IL9fU9eZADA/s400/apex_version.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/TBbeBbiAPWI/AAAAAAAADxI/IL9fU9eZADA/s1600/apex_version.jpg)

This worked, however he needed to dynamically obtain the current version for a script that he was writing. After some digging around he sent me the following query:
<pre class="brush: sql;">
SELECT * 
FROM apex_release
</pre>
<span style="font-style:italic;">Note: this isn't earth shattering but more for my reference</span>