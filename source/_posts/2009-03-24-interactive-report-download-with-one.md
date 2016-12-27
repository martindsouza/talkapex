---
title: APEX Interactive Report - Download with One Click
tags:
  - APEX
date: 2009-03-24 07:21:00
alias:
---

We've been working with Interactive Reports (IR) over the past few months and have had to do a lot of customization to meet some of our customers requirements. Over the next few weeks my posts will be focused on these customizations.

To start, here's an example of how to modify the download button in IRs. Just some background on this, we're only supporting CSV downloads now. When the clients would download IRs they would have to click the "Download" link then click on the "CSV" button. [![](http://1.bp.blogspot.com/_33EF80fk9sM/ScjenbChSKI/AAAAAAAADno/nM3A0JR76l4/s400/01_csv_image.bmp)](http://1.bp.blogspot.com/_33EF80fk9sM/ScjenbChSKI/AAAAAAAADno/nM3A0JR76l4/s1600-h/01_csv_image.bmp)

We found this extra step unnecessary so here's a way on how to do it in one step so that it will download as soon as they click "Download". We used [jQuery](http://www.jquery.com) to help out a bit. Here's a working [example](http://apex.oracle.com/pls/otn/f?p=20195:900).
<pre class="brush: js;">
$(document).ready(function() {
  $('.dhtmlSubMenuN[title="Download"]').attr('href','f?p=' + $v('pFlowId') + ':' + $v('pFlowStepId') + ':' + $v('pInstance') + ':CSV:');  
});
</pre>

Here's a detailed summary of how it works: If you scroll over the "CSV" download button you'll noticed that it looks something like : "f?p=102:3:207941080701500:CSV:". If we break that down it's really: "f?p=<APP_ID>:<APP_PAGE_ID>:<SESSION_ID>:CSV:". In Javascript you can reference each as follows:

APP_ID = $v('pFlowId');
APP_PAGE_ID = $v('pFlowStepId');
SESSION_ID = $v('pInstance');

Now to alter the "Download" link from the interactive report menu we use jQuery to identify the link: "$('.dhtmlSubMenuN[title="Download"]')" and then use the [attr function](http://docs.jquery.com/Attributes/attr#keyvalue) to set with the new URL.

<script src="#APP_IMAGES#jquery-1.3.1.min.js" type="text/javascript"></script>
<script>  
$(document).ready(function() {
  $('.dhtmlSubMenuN[title="Download"]').attr('href','f?p=' + $v('pFlowId') + ':' + $v('pFlowStepId') + ':' + $v('pInstance') + ':CSV:');  
});
</script>