---
title: How to list APEX Dictionary views using SQL
tags:
  - APEX
date: 2008-11-28 05:35:00
alias:
---

Hi,

I hope this isn't old news but it seems that whenever someone talks about the APEX dictionary they talk about going into the development application and viewing them there. You can also access the dictionary using SQL. Here's how:
<pre class="brush: sql;">
select *
from apex_dictionary
where column_id = 0
</pre>
or
<pre class="brush: sql;">
select distinct apex_view_name
from apex_dictionary
</pre>
Of course you can remove the "where column_id=0" portion and get a list of the all the columns for the views etc.
