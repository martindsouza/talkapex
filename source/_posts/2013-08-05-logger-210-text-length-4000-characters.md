---
title: Logger 2.1.0 - Text Length > 4000 Characters
tags:
  - logger
date: 2013-08-05 07:00:00
alias:
---

_In preparation for the release of Logger 2.1.0 I've decided to write a few posts highlighting some of the new features._ 

_I must thank Juergen Schuster for suggestion the [following change](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/issues/17) to Logger. He sent me an email with this request and though it took a  while to get in, I'm glad to finally see it in this release of Logger._

In the main Logger procedures the first parameter, <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">p_text</span>,  is a varchar2 data type. This means that in PL/SQL you can pass in  strings up to 32767 characters however the max table size (pre 12c) for a  varchar2 is 4000 characters. As you can imagine it caused some people  some unexpected runtime errors when they passed in large blocks of text.  The only way to get around this was either check the size of text  before logging it (if you were unsure of it's final size) or store it in  the EXTRA (clob) column.

Starting in 2.1.0, Logger  will gracefully handle text entries larger than 4000 characters. If the  text is larger than 4000 characters it will automatically be appended to  the EXTRA column and a message, "_*** Content moved to EXTRA column ***_" will be displayed in the TEXT column. I hope this resolves some unforeseen runtime errors that may have occurred in the past. 

With  12c finally released (and its ability to have varchar2 columns of 32767  length) this functionality is bound to change / become obsolete. A [new issue](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/issues/30) has been created to address this in a future release of Logger.