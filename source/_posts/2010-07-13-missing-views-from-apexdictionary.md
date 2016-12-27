---
title: Missing Views from the APEX_DICTIONARY
tags:
  - APEX
  - ORACLE APEX
date: 2010-07-13 09:00:00
alias:
---

A long time ago I wrote about [how you can list all the apex views](http://www.talkapex.com/2008/11/how-to-list-apex-dictionary-views-using.html) by querying APEX_DICTIONARY. They're some views that are still public but are not listed in the Apex Dictionary. The following query lists these views

<pre class="brush: sql;">
SELECT SYN.SYNONYM_NAME apex_view_name
  FROM all_synonyms syn, all_views av, apex_dictionary ad
 WHERE syn.synonym_name LIKE 'APEX\_%' ESCAPE '\'
   AND syn.owner = 'PUBLIC'
   AND SYN.TABLE_NAME = AV.VIEW_NAME
   AND SYN.SYNONYM_NAME = AD.APEX_VIEW_NAME(+)
   AND ad.column_id IS NULL
ORDER BY syn.synonym_name</pre>