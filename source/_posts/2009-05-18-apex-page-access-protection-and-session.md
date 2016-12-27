---
title: 'APEX: Page Access Protection and Session State Protection'
tags:
  - apex
  - security
date: 2009-05-18 09:10:00
alias:
---

APEX's Page Access Protection (PAP - For Pages) and Session State Protection (SSP - For Items) are excellent security tools to help prevent users from altering session values. What some people may not be aware of is that if you enable PAP for page it does not prevent users from altering the session state of items on that page. All it does is require that any items passed through that page via the URL require a checksum. Malicious users can still alter the item's session state using AJAX or from other pages. Long story short, if you want to lock your application down you need to enable SSP for all required items.

APEX has a great tool to do this quickly for you rather than having to go into each page item. Shared Components / Session State Protection / Page / (click page number). You can now set the PAP and the SSP for all the page items.

[![](http://4.bp.blogspot.com/_33EF80fk9sM/ShF6mp-aqQI/AAAAAAAADo4/HkfdHpHv5AY/s400/_17_PAP_SSP.bmp)](http://4.bp.blogspot.com/_33EF80fk9sM/ShF6mp-aqQI/AAAAAAAADo4/HkfdHpHv5AY/s1600-h/_17_PAP_SSP.bmp)

If you do use PAP and SSP the following queries will help you do some quick validations to ensure all your security checks are in place

<span style="font-weight:bold;">Pages without Page Access Protection</span>
<pre class="brush: sql">
SELECT aap.application_id,
       aap.application_name,
       aap.page_id,
       aap.page_name
  FROM apex_application_pages aap
 WHERE LOWER (aap.page_access_protection) = 'unrestricted'
   AND aap.application_id = :app_id
</pre>

<span style="font-weight:bold;">Page items without Session State Protection</span>
<pre class="brush: sql">
SELECT aapi.application_id,
       aapi.application_name,
       aapi.page_id,
       aapi.page_name,
       aapi.item_name
  FROM apex_application_page_items aapi
 WHERE aapi.application_id = :app_id
   AND LOWER (aapi.item_protection_level) = 'unrestricted'
</pre>

<span style="font-weight:bold;">Pages which have Page Access Protection, but have page items with no Session State Protection</span>

<span style="font-style:italic;color:red">This query helps identify pages which you think are locked down, but end users could set the session state of item values</span>
<pre class="brush: sql">
SELECT aapi.application_id,
       aapi.application_name,
       aapi.page_id,
       aapi.page_name,
       aapi.item_name
  FROM apex_application_pages aap,
       apex_application_page_items aapi
 WHERE LOWER (aap.page_access_protection) != 'unrestricted'
   AND aap.application_id = :app_id
   AND aapi.application_id = aap.application_id
   AND aap.page_id = aapi.page_id
   AND LOWER (aapi.item_protection_level) = 'unrestricted'
</pre>
