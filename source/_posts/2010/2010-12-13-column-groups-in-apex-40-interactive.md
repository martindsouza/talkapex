---
title: Column Groups in APEX 4.0 Interactive Reports
tags:
  - apex
date: 2010-12-13 19:02:00
alias:
---

A long time ago I wrote about adding Column Groups to Interactive Reports (IR) in APEX: [http://www.talkapex.com/2009/03/column-groups-in-apex-interactive.html](http://www.talkapex.com/2009/03/column-groups-in-apex-interactive.html) I created a new dynamic action plugin to do column grouping in APEX which can be downloaded [here](http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/clarifit-ir-column-grouping_73.html). Please read the help text in the plugin for full instructions on how to use it. Once integrated it should look like:

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TQbOy2c7rGI/AAAAAAAAD1U/CcFt_E75Ltw/s400/ir_col_grp.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TQbOy2c7rGI/AAAAAAAAD1U/CcFt_E75Ltw/s1600/ir_col_grp.jpg)
I also added additional JS debugging information. If you run your application in debug mode and look at the console you'll notice the following log information:

[![](http://2.bp.blogspot.com/_33EF80fk9sM/TQbOzKSdWNI/AAAAAAAAD1c/qOUzRlJuKYI/s400/ir_col_grp_logs.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/TQbOzKSdWNI/AAAAAAAAD1c/qOUzRlJuKYI/s1600/ir_col_grp_logs.jpg)
The additional logging is using the console logger wrapper that wrote: [http://www.talkapex.com/2010/08/javascript-console-logger.html](http://www.talkapex.com/2010/08/javascript-console-logger.html) If you're writing your own plugins I suggest using it to instrument your code. It helped me debug some of my JavaScript issues while developing this plugin.
