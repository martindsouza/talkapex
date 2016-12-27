---
title: Decoding Decode Data Types
tags: []
date: 2012-12-27 09:06:00
alias:
---

<div class="MsoNormal">I ran into a strange issue in APEX a while ago which was due to a decode statement not returning the data type that I expected. Here’s a sample of what the query looked like:</div><div class="MsoNormal">
</div><div class="MsoNormal"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">create or replace view mdsouza_temp as</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">select</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">&nbsp; decode(job, 'PRESIDENT', null, 0) col_decode,</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">&nbsp; case when job = 'PRESIDENT' then null else 0 end col_case</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">from emp;</span></div><div class="MsoNormal">
</div><div class="MsoNormal">At first glance you would expect both columns to be the same. If you query the view they look like they return the same values but they don’t (this is what caught me). Upon further investigation the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">COL_DECODE</span>column actually returns a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">VARCHAR2</span> and not a number:</div><div class="MsoNormal">
</div><div class="MsoNormal"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">desc mdsouza_temp;</span>

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">Name<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>Null Type<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>&nbsp;</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">---------- ---- -----------</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">COL_DECODE<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>**VARCHAR2(1)**</span></div><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">COL_CASE<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>NUMBER</span><span style="mso-spacerun: yes;"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">&nbsp;&nbsp;</span>&nbsp;&nbsp;&nbsp; </span>
<div class="MsoNormal">
</div><div class="MsoNormal">It turns out that if you have a <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">NULL</span> as the first result (not the last, default, value) the <span style="font-family: inherit;">returned value/data type</span> will be a VARCHAR2 and not the data type of the other values as shown in the following example:</div><div class="MsoNormal">
</div><div class="MsoNormal"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">create or replace view mdsouza_temp as</span></div><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">select </span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;"><span style="mso-spacerun: yes;">&nbsp; </span>decode(job, 'MANAGER', 1, 'PRESIDENT', null, 0) col_decode,</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">from emp;</span>

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">desc mdsouza_temp;</span>

<div class="MsoNormal"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">Name<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>Null Type<span style="mso-spacerun: yes;">&nbsp;&nbsp; </span></span></div><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">---------- ---- ------ </span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">COL_DECODE<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>NUMBER</span>

<div class="MsoNormal">If you do have to have <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">NULL</span> as the first result in the set you need to explicitly convert the result to the appropriate data type:</div><div class="MsoNormal">
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">create or replace view mdsouza_temp as</span></div><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">select </span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;"><span style="mso-spacerun: yes;">&nbsp; </span>to_number(decode(job, 'PRESIDENT', null, 0)) col_decode</span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">from emp;</span>
<div class="MsoNormal">
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">desc mdsouza_temp;</span>

<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">Name<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>Null Type<span style="mso-spacerun: yes;">&nbsp;&nbsp; </span></span></div><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">---------- ---- ------ </span>
<span style="font-family: &quot;Courier New&quot;,Courier,monospace;">COL_DECODE<span style="mso-spacerun: yes;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </span>NUMBER</span><span style="mso-tab-count: 1;"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">&nbsp;</span>&nbsp;&nbsp;</span>
<div class="MsoNormal">
</div><div class="MsoNormal"><span style="mso-tab-count: 1;">This is not a bug, and is covered in the [Oracle documentation on DECODE](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions049.htm#SQLRF00631) "_Oracle automatically converts the return value to the same data type as the first result._" </span></div>