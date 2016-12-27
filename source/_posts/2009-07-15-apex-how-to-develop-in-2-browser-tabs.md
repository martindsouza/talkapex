---
title: 'APEX: How to Develop in 2 Browser Tabs'
tags:
  - APEX
date: 2009-07-15 09:00:00
alias:
---

I saw this during a presentation by [Anton Nielsen](http://c2anton.blogspot.com/) last year. If you've developed with APEX for a while then you've probably wanted to have 2 developer tabs open on your browser at the same time. You'll quickly find out that this doesn't work to well.

There's an easy way around it. Lets say you're developing on an instance running on your laptop. The URL you normally go to looks something like: http://localhost:8080/apex/

Go into your hosts file (C:\WINDOWS\system32\drivers\etc\hosts for Windows users). You should see an entry like this:

127.0.0.1       localhost

Add: 127.0.0.1       giffy01 on a new line (where "giffy01" is any arbitrary name).

Your hosts file should now look like:

127.0.0.1       localhost
127.0.0.1       giffy01

In your favorite web browser,

[![](http://1.bp.blogspot.com/_33EF80fk9sM/Slv4uAHDDSI/AAAAAAAADpw/iKR9u6jqO-A/s400/reaons_for_using_ie.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/Slv4uAHDDSI/AAAAAAAADpw/iKR9u6jqO-A/s1600-h/reaons_for_using_ie.jpg) 

open the following URLs in 2 different tabs:

http://localhost:8080/apex/
http://giffy01:8080/apex/

You can now have 2 development tabs open at the same time.