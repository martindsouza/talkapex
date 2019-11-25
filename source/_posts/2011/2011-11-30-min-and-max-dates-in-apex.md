---
title: Min and Max Dates in APEX
tags:
  - apex
date: 2011-11-30 13:20:00
alias:
---

APEX 4.0 introduced native support for the [jQuery UI Date Picker](http://jqueryui.com/demos/datepicker/). When you create a date item in APEX you now have the option to set a Minimum and Maximum date as shown below. The catch is that those dates restrictions are defined when the page is rendered and aren't updated in real time. This is an issue if you want those constraints to dynamically change based on user input.

<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody><tr><td style="text-align: center;">[![](http://2.bp.blogspot.com/--ly8YwuJPOU/Ts62l3lym0I/AAAAAAAAEDU/0H3Yl9uyeXI/s320/date_picker_settings.jpg)](http://2.bp.blogspot.com/--ly8YwuJPOU/Ts62l3lym0I/AAAAAAAAEDU/0H3Yl9uyeXI/s1600/date_picker_settings.jpg)</td></tr><tr><td class="tr-caption" style="text-align: center;">Date Picker Settings</td></tr></tbody></table>
In some cases you'll need the min and max dates to change dynamically. A classic example is when you book a plane ticket and set your departure and return dates. When you select a departure date then select a return date, the return date can't be before the departure date. Dates before the departure date are usually greyed out. Using APEX you could try to implement this example by doing the following:

- Create a Date Item, P1_DEPT_DATE. Set the _Maxium Date_ to be &amp;P1_RETURN_DATE.
- Create a Date Item, P1_RETURN_DATE. Set the _Mininum Date_ to be &amp;P1_DEPT_DATE.

Run the page and select a date for the Departure Date (in this example 24-Nov-11). Next, select a Return Date. You'll notice that the calendar picker does not prevent you from selecting a return date before 24-Nov-2011.

<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody><tr><td style="text-align: center;">[![](http://4.bp.blogspot.com/-KQduHbQfqZo/Ts63sHZ2sNI/AAAAAAAAEDc/u_BtVED6DAA/s320/date_picker_restrictions.jpg)](http://4.bp.blogspot.com/-KQduHbQfqZo/Ts63sHZ2sNI/AAAAAAAAEDc/u_BtVED6DAA/s1600/date_picker_restrictions.jpg)</td></tr><tr><td class="tr-caption" style="text-align: center;">
</td></tr></tbody></table>
_Note: If you submit the page now it'll raise an exception since the Min and Max attributes requires the date to be in YYYYMMDDHH24MI format.&nbsp;_

So how do you have the min and max date restrictions change based on the user input? Excellent question. My [next post](http://www.talkapex.com/2011/12/clarifit-fromto-date-picker-plug-in.html) introduces a plugin that resolves this issue.
