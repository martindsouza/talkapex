---
title: jQuery Calendar and Application Date Format
tags:
  - APEX
date: 2008-12-14 22:54:00
alias:
---

Hi,

Last week Roel had a posting about jQuery's Date Picker: [http://roelhartman.blogspot.com/2008/12/how-to-replace-default-apex-calendar.html](http://roelhartman.blogspot.com/2008/12/how-to-replace-default-apex-calendar.html) Definitely a great example for people who are looking for an alternative to APEX's date picker.

We do use the jQuery calendar and have some interesting standards around it (I'll try to blog about this another day). The most important one, and easiest to implement, is the date format standards.

Javascript/jQuery and Oracle use different date formats. If you are going to use the jQuery date picker I strongly suggest that you standardize your date formats so that you won't have conflicting results down the road. Here's what we do:

We have 2 application level items:

F_DATE_FORMAT: is used for the application level date format
F_JQUERY_DATE_FORMAT: format to display the date the same as f_date_format

Both these items are set at login time and the values are defined in our database at the client level. You have to make sure that the date formats produce the same results.

In Roel's example he uses the jQuery on load function. For the date format just use &F_JQUERY_DATE_FORMAT. instead and this will load set the jQuery date formats accordingly.

Here's a listing of jQuery's date formats: http://docs.jquery.com/UI/Datepicker/%24.datepicker.formatDate

And Oracle's date formats: http://www.techonthenet.com/oracle/functions/to_date.php

Hope this helps,

M
