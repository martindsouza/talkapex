---
title: APEX Page Locks
tags:
  - APEX
  - ORACLE APEX
date: 2010-09-29 07:21:00
alias:
---

When developing APEX applications in a team environment you usually don't want multiple developers working on the same page at the same time. This can cause a lot of confusion and unexpected effects with the application.

APEX has a great feature called Page Locks which enables developers to lock a page so that they're the only one that can work on it at a given time. Other developers can view the page, but can not make any changes while the page is locked.

To lock a page, click on the lock icon located in the top right corner of the page as displayed in the following picture:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TKM8h_AOwGI/AAAAAAAAD0o/h4QIeqGXmwA/s400/Page+Locks.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TKM8h_AOwGI/AAAAAAAAD0o/h4QIeqGXmwA/s1600/Page+Locks.jpg)
You will need to enter a comment and then click the Lock Checked button. For the comment it is recommended that you enter what case/bug number you're working on and what impact it may have on the page. The history of these comments, both for locking and unlocking the page, is logged. Before you invest a lot of time with detailed comments it's important to note the following points that may be relevant within your environment. 

Page locks are not retained when an application is copied or exported. This makes sense as you don't want page locks to propagate in an export file.

Like page locks, the lock history is not retained when an application is copied or exported. If you tend to move the development copy of your application between workspaces this may be an issue. This may effect the level of detail for lock comments that you require the developers on your team to use.

If you want to backup the page lock comment history you can access them from the following table:
<pre class="brush: sql">
  SELECT *
    FROM apex_040000.wwv_flow_lock_page_log
   WHERE lock_flow = :app_id
ORDER BY lock_page, action_date
</pre>