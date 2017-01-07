---
title: 'APEX: $x_disableItem'
tags:
  - apex
  - javascript
date: 2009-07-30 09:00:00
alias:
---

I had an issue where I disabled a radio button (after selecting a value) using the APEX JavaScript function ([$x_disableItem](http://download.oracle.com/docs/cd/E10513_01/doc/apirefs.310/e12855/javascript_api.htm#CHDDAHDJ)) but my selected value wasn't being saved.

After some testing I noticed that if I selected a value, disabled the radio, then submitted the page item's value would be null (empty string).

[![](http://4.bp.blogspot.com/_33EF80fk9sM/SnDhTxFCoLI/AAAAAAAADp8/_zigDWvuY9E/s400/disableItem_01.bmp)](http://4.bp.blogspot.com/_33EF80fk9sM/SnDhTxFCoLI/AAAAAAAADp8/_zigDWvuY9E/s1600-h/disableItem_01.bmp)
You can view an example of this here: [http://apex.oracle.com/pls/otn/f?p=20195:2200](http://apex.oracle.com/pls/otn/f?p=20195:2200)

They're several ways to fix this problem. The easiest would be to "<span style="font-style:italic;">undisable</span>" (i.e. re-enable) the item before the page is submitted. I know "<span style="font-style:italic;">undisable</span>" is not a word but to re-enable a page item you need to call the disable function: $x_<span style="font-weight:bold;">disable</span>Item('PX',<span style="font-weight:bold;">false</span>);
