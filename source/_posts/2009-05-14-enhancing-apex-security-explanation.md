---
title: Enhancing APEX Security - Explanation
tags:
  - apex
  - security
date: 2009-05-14 08:16:00
alias:
---

I recently posted an entry called [Enhancing APEX Security](http://apex-smb.blogspot.com/2009/05/enhancing-apex-security.html). I may have jumped the gun and not given a full explanation as to what problem this was trying to solving and the logic behind this solution. Here's the full explanation to the problem and a high level overview of the proposed solution.

In APEX, Session State Protection (SSP) can help secure your applications by preventing users to set session state value of items. Developers should be aware that it has 2 issues regarding it. First, it prevents items from being set using AJAX. If I try to set using AJAX I get the following error (using Firebug):

[![](http://4.bp.blogspot.com/_33EF80fk9sM/SgwoKsULsHI/AAAAAAAADog/9ZSr7t3nPbw/s400/_15_folloup_1.bmp)](http://4.bp.blogspot.com/_33EF80fk9sM/SgwoKsULsHI/AAAAAAAADog/9ZSr7t3nPbw/s1600-h/_15_folloup_1.bmp)

Second, when a user is on a page, they can alter the value before it is submitted to try to access data they aren't allowed to. For example, let's say users are only allowed to see information about people within their own department. So my LOV is something like:

<pre class="brush: sql">
SELECT e.ename d,
       e.empno r
  FROM emp e
WHERE job = 'CLERK'
</pre>

Using Firebug I can see that this is what my option list looks like

[![](http://4.bp.blogspot.com/_33EF80fk9sM/Sgwoe5C-a5I/AAAAAAAADoo/ujojjsZow68/s400/_15_followup_2.bmp)](http://4.bp.blogspot.com/_33EF80fk9sM/Sgwoe5C-a5I/AAAAAAAADoo/ujojjsZow68/s1600-h/_15_followup_2.bmp)

Now what if I were to modify the select list and change the value for "SMITH" from 7369 to 7839 (This is "KING" / President) then submit the page? It still works and sets the value in session state:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/SgwomQs7-1I/AAAAAAAADow/j87nrZ3affs/s400/_15_followup_3.bmp)](http://3.bp.blogspot.com/_33EF80fk9sM/SgwomQs7-1I/AAAAAAAADow/j87nrZ3affs/s1600-h/_15_followup_3.bmp)

Notice how the value in session is set to 7839, which we really shouldn't have access to? If you're not careful in your application this can lead to serious security holes.

Now on to the proposed solution. When you have a Select List, use a hashed value for the IDs so that users can't alter the select list values (well they can but they'll require the hash value as well). Instead of P1_EMPNO being a select list, set it as a Hidden and Protected item and enable SSP for it. This allows your application to still reference P1_EMPNO as it normally would. Create a new item, P1_EMPNO_SEC, which will be a select list with the altered LOV code (i.e. the return value has a hash associated to it). So if the value is 7369, it will actually be 7369@J92388502378540C (i.e. VALUE[DELIMITER][HASH]). The code (in my [example](http://apex-smb.blogspot.com/2009/05/enhancing-apex-security.html)) handles the rest so that when a user submits a page, it will take the submitted value from the secure item and then set the "regular" item with the unhashed value. If someone were to try to alter the select list, they'd be unsuccessful since the unhashing method returns NULL when an invalid hash value is present.

The reason why I create 2 items is I don't want to have to alter any existing code. So all code referencing P1_EMPNO will remain the same. If users need to pass P1_EMPNO in via the URL they can (provided they pass in the checksum since it has SSP enabled).
