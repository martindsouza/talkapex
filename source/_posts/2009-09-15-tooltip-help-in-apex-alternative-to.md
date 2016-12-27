---
title: 'Tooltip Help in APEX: An alternative to popup help'
tags:
  - APEX
  - ORACLE
date: 2009-09-15 09:00:00
alias:
---

The default help functionality for APEX is ok but can cause some problems with users browsers since it uses popup windows to display the help. If you do some digging around regarding web development best practices you'll find a lot of articles discussing why you should avoid popup windows. Instead of using the default help popup windows I prefer to use tooltips to display the help. Besides avoiding the popup window, tooltips generate a good user experience by displaying item help very quickly.

This demo uses a jQuery tooltip plugin: [http://bassistance.de/jquery-plugins/jquery-plugin-tooltip/](http://bassistance.de/jquery-plugins/jquery-plugin-tooltip/). Please visit the Bassistance website to find out how to configure the look and feel of the tool tips.

Here's a link to the demo: [http://apex.oracle.com/pls/otn/f?p=20195:2400](http://apex.oracle.com/pls/otn/f?p=20195:2400)

[![](http://1.bp.blogspot.com/_33EF80fk9sM/Sq0Cb8Ae2WI/AAAAAAAADqc/flO0nQATWCA/s400/tooltiphelp.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/Sq0Cb8Ae2WI/AAAAAAAADqc/flO0nQATWCA/s1600-h/tooltiphelp.jpg)

<span style="font-weight:bold;">- Create or Update Label Template</span>
<span style="font-style: italic;">Note: You don't need to create a new template. If you want to, just update the existing templates</span> 
- Copy the "Optional Label with Help" and rename to "Optional Label with ToolTip".
<span style="font-style: italic;">Note: You can do this for required labels as well</span>

- Change the "Before Label" From:
&lt;label for="#CURRENT_ITEM_NAME#" tabindex="999"&gt;&lt;a class="t20OptionalLabelwithHelp" href="javascript:popupFieldHelp('#CURRENT_ITEM_ID#','&SESSION.')" tabindex="999"&gt;

To:
<span style="font-style: italic;">I removed the href reference and replaced with #</span>
&lt;label for="#CURRENT_ITEM_NAME#" tabindex="999"&gt;&lt;a class="t20OptionalLabelwithHelp" href="#" tabindex="999"&gt;

<span style="font-weight:bold;">- Create a HTML region</span>
<span style="font-style: italic;">This can be done on P0 to load for each page</span>
<span style="font-style: italic;">Don't forget to upload the [jQuery](http://jquery.com/) and [tooltip](http://bassistance.de/jquery-plugins/jquery-plugin-tooltip/) JS files into Shared Components / Static Files</span>
<pre class="brush: html">
<script src="#APP_IMAGES#jquery-1.3.2.min.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.tooltip.pack-1.3.js" type="text/javascript"></script>

<script type="text/javascript">

$(document).ready(function(){
  $('span.itemToolTip').each(function(i){
    $('label[for="' +  $(this).attr('foritem') + '"]').attr('title',$(this).html()).tooltip({ 
        track: true, 
        delay: 0, 
        showURL: false, 
        showBody: " - ", 
        fade: 250 
      });
  });
  // Remove Original ToolTip Help Text
  $('span.itemToolTip').remove();
});
</script>

<style>
#tooltip {
 position: absolute;
 z-index: 3000;
 border: 1px solid #111;
 background-color: #eee;
 padding: 5px;
 opacity: 0.85;
}
#tooltip h3, #tooltip div { margin: 0; }
</style>
</pre>

<span style="font-weight:bold;">- Create an Application Process</span>
Name: AP_GET_HELP_TEXT
Process Point: On Load: After Header

<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT    '<span class="itemToolTip" foritem="'
                   || item_name
                   || '">'
                   || item_help_text
                   || '</span>' help_html
              FROM apex_application_page_items
             WHERE application_id = :app_id
               AND page_id = :app_page_id
               AND item_help_text IS NOT NULL)
  LOOP
    HTP.p (x.help_html);
  END LOOP;
END;
</pre>

<span style="font-weight:bold;">- Change Item Labels</span>
Change Item labels to "Optional Label with ToolTip". Only do this if you created a new template