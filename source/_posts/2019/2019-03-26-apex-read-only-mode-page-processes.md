---
title: 'APEX Read Only Mode: Page Processes'
tags:
  - apex
date: 2019-03-26 11:15:01
alias:
---



APEX has the ability to easily have Read Only (RO) pages. The screen shot below shows how to define the page's RO attribute. For this demo it's set to `Always` but it could use any of the usual conditions in APEX.

{% asset_img page-attribute.png %}

When the page is loaded, all its page items are not editable:

{% asset_img p2_name_ro.png %}

They're a few things to consider when using the Read Only attribute for a page:

## Hacking a page item with JavaScript

In the previous image I can not modify the page item by standard means. I can still modify it using JavaScript (JS): `apex.item('P2_NAME').setValue('martin');` Submitting the page will trigger an error (which is good):

{% asset_img js-modify-page-item.png %}

This information is relevant as you should probably disable any Dynamic Actions (DA) that modify page items when the page is in Read Only mode. Conditionally running DAs can easily be done by applying the declarative condition to a DA `Page/Region is Read Only`. You could also use the [`apex_page.is_read_only`](https://docs.oracle.com/database/apex-18.1/AEAPI/IS_READ_ONLY-Function.htm#AEAPI30179) if you have additional server-side conditions for a DA.

## Page can still be submitted

In this demo, a page process has been created that logs a message using [Logger](https://github.com/oraopensource/logger).

{% asset_img page-process %}

If a user clicks the `Save` button the process is still run, despite the page being in Read Only mode. The next logical thing to do is to put a condition on the `Save` button so it does not appear when the page is in Read Only mode. Users can bypass this by running `apex.page.submit('SAVE');` in the console. The following demo highlights these concerns:

{% asset_img demo-save-no-restrictions.gif %}

Similar to the previous section, if your page uses the Read Only attribute you should also apply conditions on the corresponding processes to restrict them when the page is in Read Only mode.
