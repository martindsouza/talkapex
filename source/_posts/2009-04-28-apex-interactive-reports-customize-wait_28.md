---
title: APEX Interactive Reports - Customize Wait Display Part 2
tags:
  - APEX
date: 2009-04-28 22:57:00
alias:
---

APEX Interactive Reports - Custom Wait Display Part 2

<span style="font-style:italic;">Note: If you're using APEX 4.0 please see this post: [http://www.talkapex.com/2010/08/apex-40-interactive-reports-customize.html](http://www.talkapex.com/2010/08/apex-40-interactive-reports-customize.html)</span>

A few weeks ago I wrote about customizing the default APEX IR wait logo: [http://apex-smb.blogspot.com/2009/04/apex-interactive-reports-customize-wait.html](http://apex-smb.blogspot.com/2009/04/apex-interactive-reports-customize-wait.html). Here is the second part of that article which focuses on overwriting APEX's  <span style="font-style:italic; font-weight:bold;">dispIRBusyGraphics</span> function.

Overriding the default "Busy Graphic" function can be used to customize messages/images as well as preventing users from resubmitting IR request which may take a long time.

An example of this can be found [here](http://apex.oracle.com/pls/otn/f?p=20195:1400)

<span style="font-weight:bold;">- 1: Create IR Report</span>

<pre class="brush: sql">
-- Trying to make this a slow query to demonstrate the IR loader
SELECT     e.*, sum(e.sal) over() test 
      FROM emp e
CONNECT BY LEVEL <= 5
</pre>

<span style="font-weight:bold;">- 2: Download jQuery and Simple Modal and upload the JavaScript files in the Application's Static Files:</span>

jQuery: [http://jquery.com/](http://jquery.com/)
Simple Modal: [http://www.ericmmartin.com/projects/simplemodal/](http://www.ericmmartin.com/projects/simplemodal/)

<span style="font-weight:bold;">- 3: Create an HTML region and add the following:</span>

<pre class="brush: html">
<script src="#APP_IMAGES#jquery-1.3.2.min.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.simplemodal-1.2.3.js" type="text/javascript"></script>
<script type="text/javascript">
/**
 * @param pRegionStaticId ID of region to set to modal
 * @param pOptions Options for modal Screen. See: http://www.ericmmartin.com/projects/simplemodal/#options for more info
 */
goModal=function(pRegionStaticId, pOptions){
  var vDefaults = {persist: true, overlayCss: {backgroundColor: '#606060'}}; // Note: It's important that you leave the persist = true otherwise items values will be cleared

  pOptions = jQuery.extend(true,vDefaults, pOptions);

  // To maintain order of APEX items (see forum posting above
  $('#' + pRegionStaticId).wrap('<div></div>'); 

  // Make sure the region is visible
  $('#' + pRegionStaticId).show();

  // Open Modal Screen
  $('#' + pRegionStaticId).modal(pOptions);  
}// goModal

/**
 * Closes the modal screen
 */
modalClose=function(){
  $.modal.close();
}// modalClose

// OnLoad tasks
$(document).ready(function(){
  // Only apply if IR are present for page
  if ($('.apexir_WORKSHEET_DATA').length > 0) {
    // See apex_ns_3_1.js for _BusyGraphic
    function dispIRBusyGraphics(pState){
      if(pState == 1){
        // Here apexir_LOADER is the object ID. You can use your own region if you wanted to etc...
     goModal('apexir_LOADER', {position:['30%',]});
    }
    else{
          modalClose();
      }
    return;
    }// dispIRBusyGraphics

    function updateIRJS(){
     // This time out is required since after the report is refreshed via AJAX, need to reattach the l_LastFunction command
     setTimeout(
       function(){
        gReport._BusyGraphic = function(pState){dispIRBusyGraphics(pState);};
       },
       1000
      );
    }

    gReport = new apex.worksheet.ws('');
    gReport.l_LastFunction = function(){dispIRColGroups();}
    // Need to put timeout since not registering on initialization
    setTimeout(function(){gReport._BusyGraphic = function(pState){dispIRBusyGraphics(pState);};},500);
    updateIRJS();
  } //If IR exist
});
</script>

</pre>