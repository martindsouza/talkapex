---
title: Adding a Refresh Button for Standard Reports in APEX
tags:
  - apex
date: 2019-03-20 13:38:14
alias:
---



While developing standard reports in APEX I usually do the following repetitive steps. Modify something to do with the report (query, setting, attributes, source data, etc). After saving or committing my changes I refresh the page to see how it looks. This gets frustrating when doing a lot of small edits as a full page refresh is not required.

I recently created a script to solve this problem. The script adds a small refresh icon in the first column header of each standard report. Clicking it will refresh the current report. The following demo shows it in action and how you don't need a full page refresh to view changes to a report: _(note: you don't need to run this in the console, which will be discussed at the end of the post)_

{% asset_img standard-report-refresh.gif %}

The following steps outline how to integrate this directly in your application:

- On Page 0, create a new Dynamic Action (DA)
  - `When > Event`: Page Load
    - `Action > True > JavaScript`:

```js
var buttonHtml = '<span aria-hidden="true" class="apex-dev-refresh fa fa-refresh fa-anim-spin"></span>';

$('.t-Report').each(function(){
  var $this = $(this);

  // Add button to screen
  $this.find('th:first').prepend(buttonHtml);

  // Regenerate the refresh button after a region refresh
  $('#' + $this.data('regionId')).on('apexafterrefresh',function(){
    $(this).find('th:first').prepend(buttonHtml);
  })

})

// Register event listener
$(document).on('click', ".apex-dev-refresh", function(){
  $(this).closest('.t-Report').trigger('apexrefresh');
});
```

The DA should look like this:

{% asset_img p0-da-setup.png %}

You can change the icon and its settings to anything you want (i.e. by modifying the `buttonHtml`). You can use the [APEX Icon Generator](https://apex.oracle.com/pls/apex/f?p=42:4000:::NO:::) tool to generate the code for a new icon.

Though not required, I **highly recommend** you add a Build Option (read [Scott Wesley's](https://twitter.com/swesley_perth) [article](http://www.grassroots-oracle.com/2010/06/oracle-apex-build-options.html) - I have a Build Option called `DEV_ONLY`) to restrict the DA to development only.

You could add the code to a JavaScript file and include it with your application or create a Dynamic Action plugin. _(If you create a plugin for this please let me know and I'll be sure to refreence it._


