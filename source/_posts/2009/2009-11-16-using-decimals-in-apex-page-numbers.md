---
title: Using Decimals in APEX Page Numbers
tags:
  - apex
date: 2009-11-16 09:30:00
alias:
---

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SwCbWN630kI/AAAAAAAADtg/G-w443rjVeQ/s200/800px-999_Perspective.png)](http://2.bp.blogspot.com/_33EF80fk9sM/SwCbWN630kI/AAAAAAAADtg/G-w443rjVeQ/s1600/800px-999_Perspective.png)

When creating an APEX page you can use decimals in the page number. If you logically group your pages by page numbers and run out of page numbers this may be useful. For example if you create pages 1 through 10 then realize you'd like a page right beside page 5, you could create Page 5.1.

I don't personally recommend using decimals in page numbers as you can't use decimals in item names, so <span style="font-style:italic;">P5.1_X</span> won't work. Of course you may find some other use for creating pages with decimals.

If you're concerned about logically grouping pages for your development try initially separating pages by increments of 100 or by using Page Groups. To configure Page Groups go to the main application development page and click on "Page Groups" on the right hand side. Once you've setup the group and assigned pages, go back to the main development page and under the "View" drop down select "Groups".
