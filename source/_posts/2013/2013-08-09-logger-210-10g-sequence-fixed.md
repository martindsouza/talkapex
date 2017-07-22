---
title: Logger 2.1.0 - 10g Sequence Fixed
tags:
  - logger
date: 2013-08-09 07:00:00
alias:
---

_In preparation for the release of Logger 2.1.0 I've decided to write a few posts highlighting some of the new features._

Apparently not too many people who use Logger are still running Oracle 11gR1 or lower since I was only notified of this issue now. Patrick Jolliffe was kind enough to point out that there was an [issue](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/issues/26) when installing Logger in a 10g. It turns out that  `:new.id := logger_logs_seq.nextval;` doesn't work in 10g. It only works in 11gR2 and above. Clearly it's been a while since I've used 10g, or better yet 11gR1.

The ability to reference sequences `nextval` in PL/SQL is mentioned in the 11gR2 [New Features guide](http://docs.oracle.com/cd/B28359_01/server.111/b28279/chapter1.htm#FEATURENO07450).

Logger 2.1.0 address this issue by only supporting the old `select into...`  technique for various reasons. At first I was skeptical about  performance impacts of having the context switch, however it appears  that behind the scenes if called from PL/SQL it does an internal select  into to obtain the value. [Connor McDonald](http://connormcdonald.wordpress.com/) has some good slides about this [here](http://www.oracledba.co.uk/perth08/mcdonald_oct08_11g_developers.pdf) (see page 59 onwards).

Thanks to [Dan McGhan](https://twitter.com/dmcghan) and [Scott Wesley](https://twitter.com/swesley_perth) for helping sort this issue out.
