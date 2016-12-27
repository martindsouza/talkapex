---
title: Session State Protection in Detail
tags: []
date: 2012-11-19 20:48:00
alias:
---

Session State Protection (SSP) can be confusing for new APEX developers and also difficult for senior APEX developers trying to explain it. This article will cover SSP in detail.

**What is SSP?&nbsp;**

SSP is used to prevent URL tampering. URL tampering is when a malicious user modifies URL parameters to access data that they may not have access to. For example if you have a Master Detail setup for the EMP table (i.e  a report with edit links to a form) each link will look something like this: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">http://fcapex42.clarifit.com:8894/apex/f?p=211:11:14916146169058::::**P11_EMPNO:7698**</span>. If you restricted the report to employees in your current department you can easily change the EMPNO id and change it to another employee in another department. Even though the intention of the application was to “restrict” users to only edit people within their own department a malicious or curious user could start editing all the employees.

SSP prevents URL tampering by applying a checksum to the end of the URL. The new URL looks something like: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">http://fcapex42</span><span style="font-family: &quot;Courier New&quot;,Courier,monospace;"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">.clarifit.com</span>:8894/apex/f?p=211:11:14916146169058::::P11_EMPNO:7698&amp;**cs=3358AAB32787C2A2B294CE934D50FD0C3**</span>. If a user now tries to modify the EMPNO id they will get an error in APEX as the URL won't have the correct checksum.

**Applying SSP**

To add SSP at the page level (technically called Page Access Protection at the page level) edit the page and scroll down to the Security section. Select Arguments Must Have Checksum for the Page Access Protection option.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-02VsYYqkLjQ/UKr7NFcz3AI/AAAAAAAAERY/hHHHakbXrC8/s320/page_access_protection.png)](http://4.bp.blogspot.com/-02VsYYqkLjQ/UKr7NFcz3AI/AAAAAAAAERY/hHHHakbXrC8/s1600/page_access_protection.png)</div>Once that is added you can modify the page items you want SSP to be applied to. Edit a page item and scroll down to the Security section. Select an option for Session State Protection (note: click on the help link to find the differences between the various options; I usually use Checksum Required - Session Level).

<div class="separator" style="clear: both; text-align: center;">[![](http://1.bp.blogspot.com/-mNipLR_CfpI/UKr7NRtGssI/AAAAAAAAERc/xy1eDMPKQJA/s400/session_state_protection.png)](http://1.bp.blogspot.com/-mNipLR_CfpI/UKr7NRtGssI/AAAAAAAAERc/xy1eDMPKQJA/s1600/session_state_protection.png)</div>
To modify a page and all its page items at the same time go to Shared Components &gt; Session State Protection &gt; Page Item and click on a page link.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-9alTXvzvgck/UKr7Nrt1KxI/AAAAAAAAERo/ZRhi4oKVgOg/s400/secure_page_and_page_items.png)](http://3.bp.blogspot.com/-9alTXvzvgck/UKr7Nrt1KxI/AAAAAAAAERo/ZRhi4oKVgOg/s1600/secure_page_and_page_items.png)</div>
**Two Layered Approach: Page vs Page Items** 

The easiest way to think of SSP in APEX is that it’s a two layered approach. First you must define which pages (not page items) require a checksum applied to it when passing in URL parameters. The second part is to define which page items require a checksum when set from the URL. You can’t do the later with out the former (i.e. if you just define a set of page items to have SSP it won’t work) but you can have pages with SSP without any page items with SSP.

The two layered approach is where most people get confused. If you apply SSP at the page level, shouldn’t all items be protected? At first glance the answer is “yes”. But the actual answer is no. The following example highlights why both “layers” are required.

Suppose we only apply Page Access Protection for the EMP form page (let’s say P11). If I clicked on a link to edit an employee it would add the checksum. Initially it looks as if everything is ok. Now suppose we have another page, P20, that doesn’t have Page Access Protection enabled. We could actually set P11_EMPNO from P20\. The URL would look like this: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">http://fcapex42</span><span style="font-family: &quot;Courier New&quot;,Courier,monospace;"><span style="font-family: &quot;Courier New&quot;,Courier,monospace;">.clarifit.com</span>:8894/apex/f?p=211:**20**:14916146169058::::**P11_EMPNO**:7839</span>  Most people forget that you can set page items from any page, i.e. you’re not restricted to only setting items for the current page that you’re accessing.

You could also set any of the page items via an AJAX call (since none of them have SSP applied to them). Either way, just applying page access protection isn't enough.

**When Not to Apply SSP**

If you haven’t implemented SSP in your application you should really look at doing so. Before you apply it to all items it’s important to note that any items that can be set via an AJAX call can not have SSP enabled for them. The most usual case of this is cascading LOVs (select a department, then a list of employees gets refreshed with all the employees that belong to the selected department).

The reason why you can’t have SSP item items that are set via AJAX is that AJAX uses JavaScript to build the URL. Since JavaScript code is downloaded and runs on the end user’s machine it is not deemed to be secure. So if you had your checksum code in a JavaScript file a malicious user to easily reverse engineer it and apply checksums for any item they wanted to.

**Conclusion**

SSP is a great feature to quickly help secure your application. It’s important to remember that SSP only prevents URL tampering. Nothing more, nothing less. It’s a common mistake by developers, and managers alike, that just applying SSP means that they’ve locked down and secured an application. It’s just one of many steps to help protect your application.