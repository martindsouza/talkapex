---
title: apex_application.stop_apex_engine
tags:
  - apex
  - plsql
date: 2011-12-05 07:00:00
alias:
---

Something that was upgraded in APEX 4.1 with little to no far fair was the addition of a new (supported) procedure called <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">apex_application.stop_apex_engine</span>. The <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">apex_application.stop_apex_engine</span> essentially stops the APEX engine and, as a result, will stop processing the rest of the page. It is most commonly used when forcing URL redirects from a page.

Before APEX 4.1 you could still use a similar feature however it was an unsupported variable in the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">apex_application</span> package called <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">g_unrecoverable_error</span>.

I've included an example below of the before vs. after 4.1 ways to stop the APEX engine.
<pre class="brush: sql; highlight: [4, 9]">-- Pre-APEX 4.1 (not supported)
...
owa_util.redirect_url('http://www.clarifit.com');
apex_application.g_unrecoverable_error:= true; -- End APEX session

-- APEX 4.1 and above (supported)
...
owa_util.redirect_url('http://www.clarifit.com');
apex_application.stop_apex_engine; -- End APEX session
</pre>
For those who have used the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">apex_application.g_unrecoverable_error</span> variable in some of your applications you should go back and upgrade to the new procedure. For more information about the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">stop_apex_engine</span> procedure please read the APEX [documentation](http://docs.oracle.com/cd/E23903_01/doc/doc.41/e21676/apex_app.htm#BABGIDII).

Thanks to Jason Long for posting the following comment about the **differences between <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">apex_application.g_unrecoverable_error</span> and <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">stop_apex_engine</span>**. Be sure to read it carefully because in the right conditions switching between the two calls can have significant impacts.

_There appear to be a couple gotchas with simply replacing g_unrecoverable_error with stop_apex_engine.

1\.  If there is any PL/SQL code immediately following the  g_unrecoverable_error it will still be executed. It looks like  stop_apex_engine actually exits out of the routine.

2\. It looks  like stop_apex_engine will roll back any SQL inserts/updates/deletes  that occur in the routine, where as g_unrecoverable_error allow them to  be committed. _
