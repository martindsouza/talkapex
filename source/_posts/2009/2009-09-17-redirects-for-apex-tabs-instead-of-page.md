---
title: Redirects for APEX Tabs instead of page submits
tags:
  - apex
date: 2009-09-17 09:00:00
alias:
---

With APEX Tabs you may not want the page to be submitted each time the user clicks on a tab. If you look at the link for the tabs they look like: <span style="font-style:italic">javascript:doSubmit('xxxx');</span> Where "xxx" is the name of the tab. "doSubmit" will submit the page and could trigger page computations, validations, and processes. If all you want to do is use the tabs as a form of navigation (i.e. when you click on a tab it redirects to another page) then this could cause some problems.

To avoid triggering Page Processing on tabs you can modify all the conditions on your Page Process (this could be very long) or change the link. They're multiple ways to change the links, here's a simple one using [jQuery](http://jquery.com).

Here's a link to the demo: [http://apex.oracle.com/pls/otn/f?p=20195:2500](http://apex.oracle.com/pls/otn/f?p=20195:2500). Note, in the demo I don't trigger the javascript to change the link automatically so you can see what they look like before and after changing the links.

<span style="font-weight:bold;">- Create an Application Process</span>
<span style="font-style:italic">Note: You may want to only apply this to certain pages depending on the use case</span>

Name: AP_UNSUBMIT_TABS
Process Point: On Load: After Header
<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT tab_name,
                   tab_page,
                      '<span class="apexTabURLs" tabname="'
                   || tab_name
                   || '" tabnewurl="'
                   || apex_util.prepare_url (   'f?p='
                                             || :app_id
                                             || ':'
                                             || tab_page
                                             || ':'
                                             || :app_session
                                             || ':'
                                             || tab_name
                                             || '::'
                                             || tab_page
                                             || '::')
                   || '"></span>' tabinfo
              FROM apex_application_tabs t
             WHERE application_id = :app_id)
  LOOP
    HTP.p (x.tabinfo);
  END LOOP;
END;
</pre>

<span style="font-weight:bold;">- Create a HTML region</span>
<span style="font-style:italic">Note: You'll need to install the jQuery JS file in Shared Components / Static Files</span>

<pre class="brush: html">
<script src="#APP_IMAGES#jquery-1.3.2.min.js" type="text/javascript"></script>
<script type="text/javascript">
  $('span.apexTabURLs').each(function(i){
    var vTabName = $(this).attr('tabname');
    var vNewUrl = $(this).attr('tabnewurl');
    $('#t20Tabs a[href="javascript:doSubmit(\'' + vTabName + '\');"]').attr('href',vNewUrl);
  });
  // Remove the extra span tags
  $('span.apexTabURLs').remove();
</script>  
</pre>
