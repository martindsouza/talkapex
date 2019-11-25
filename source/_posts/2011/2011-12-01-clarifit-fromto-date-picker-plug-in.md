---
title: ClariFit From/To Date Picker Plug-in
tags:
  - apex
  - apex-plugin
date: 2011-12-01 07:03:00
alias:
---

In my [previous post](http://www.talkapex.com/2011/11/min-and-max-dates-in-apex.html) I discussed an issue about dynamic min and max dates using the APEX date picker. If you haven't read the article please take a few minutes before continuing.

At the end of my [previous post](http://www.talkapex.com/2011/11/min-and-max-dates-in-apex.html) I left off with the following question: How do you solve the dynamic min/max date issue? The answer is to use a new plugin that I created called [ClariFit From To Date Picker](http://apex.oracle.com/pls/apex/f?p=45587:700:0::NO:::). This is a free and open source plugin created as part of the [ClariFit](http://www.clarifit.com/) plugin set and is covered in detail in the book: [Expert Oracle Application Express Plugins](http://goo.gl/089zi).

The plugin is an item plugin. Its options (shown below) are similar to the APEX date picker. The main difference is that instead of having Minimum and Maximum date attributes you now have Date Type and Corresponding Date Item attributes. The Date Type can either be a From or To date (i.e. min/max date). The Corresponding Date Item is the item name for the date picker that will determine the date's restrictions.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-f8_DJfxd6CM/Ts6_686oU4I/AAAAAAAAEDs/nKfmswd-1vU/s1600/plugin_date_picker_settings.jpg)](http://4.bp.blogspot.com/-f8_DJfxd6CM/Ts6_686oU4I/AAAAAAAAEDs/nKfmswd-1vU/s1600/plugin_date_picker_settings.jpg)</div>
I've created a demo on [plugins.clarifit.com](http://apex.oracle.com/pls/apex/f?p=45587:700:0::NO:::). If you select a date in the From Date field (in this case 24-Nov-2011) then open the To Date date picker you'll notice that you can't select anything before 24-Nov-2011 as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-RBnZdaHdxbg/Ts6_6p7r0XI/AAAAAAAAEDk/K0C2JsQstI4/s1600/plugin_date_picker.jpg)](http://3.bp.blogspot.com/-RBnZdaHdxbg/Ts6_6p7r0XI/AAAAAAAAEDk/K0C2JsQstI4/s1600/plugin_date_picker.jpg)</div>
The plugin also comes bundled with some nifty features that may not be visible at first but are really useful:

*   Allows for different From/To date formats (i.e. they don't need to be the same).&nbsp;*   Built in validations:

*   Validates that the input is a valid date

*   Validates that From Date is less than or equal to the To Date*   Javascript Console instrumentation (run the page in debug mode and look at the console)_Note: The built in validations will run as part of the page's validations and are only executed if you enable validation as part of your submit button._

The plugin can be downloaded from [apex-plugin.com](http://apex-plugin.com/oracle-apex-plugins/item-plugin/clarifit-from-to-date-picker_154.html)._ _
