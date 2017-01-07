---
title: 'APEX IR: Subscriptions with Bind Variables and VPD'
tags:
  - apex
date: 2010-09-15 09:00:00
alias:
---

Starting in APEX 4.0, Interactive Reports (IR) now have the ability for users to automatically get reports emailed to them. This is a great feature to allow end users to subscribe to data extracts rather than developers writing custom code.

To enable subscriptions select the Subscription option in the IR Report Attributes page:

[![](http://4.bp.blogspot.com/_33EF80fk9sM/TIzmL7f2yxI/AAAAAAAADzY/9eV46ilavIU/s400/ir_enable_subscription.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/TIzmL7f2yxI/AAAAAAAADzY/9eV46ilavIU/s1600/ir_enable_subscription.jpg)
Users can subscribe to email notifications by selecting the Subscription option in the Actions menu

[![](http://4.bp.blogspot.com/_33EF80fk9sM/TIzmMSOEcnI/AAAAAAAADzg/btDT4G-g-9s/s400/ir_subscription.jpg)](http://4.bp.blogspot.com/_33EF80fk9sM/TIzmMSOEcnI/AAAAAAAADzg/btDT4G-g-9s/s1600/ir_subscription.jpg)
[![](http://1.bp.blogspot.com/_33EF80fk9sM/TIzmMl2wf8I/AAAAAAAADzo/V0LpqLxYxcg/s400/ir_subscription_subscribe.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TIzmMl2wf8I/AAAAAAAADzo/V0LpqLxYxcg/s1600/ir_subscription_subscribe.jpg)
Before implementing subscriptions they're some things that you should be aware of.

If your query uses bind variables, they will not be bound in the emailed reports. For example, supposed you had a select list of departments, <span style="font-style:italic;">P1_DEPTNO</span>. If you then had an IR that listed all the employees in the department it would look like:

<pre class="brush: sql">
SELECT *
  FROM emp
 WHERE deptno = :p1_deptno
</pre>
When a user is viewing the report in the application they'll get some employees for each department. If they subscribe to this IR then they'll get no rows returned as no value is bound for :P1_DEPTNO.

If you use the VPD feature in Oracle (Shared Components > Security Attributes > Virtual Private Database) it doesn't appear to be fired before the query is run as part of the subscription. I tested this by calling a log procedure in the VPD section. When the report is generated for an email subscription no log was registered in my log table.

I wouldn't classify either of these cases as bugs, however it's important for you to know what subscriptions can and can't handle before leveraging the subscriptions feature in a production application.
