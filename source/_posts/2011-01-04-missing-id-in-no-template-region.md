---
title: Missing ID in the No Template Region Template
tags:
  - apex
date: 2011-01-04 08:00:00
alias:
---

When you create or edit a region in APEX you can select a template. Region templates define the location of buttons in a region, how the region looks, etc. There is a  region template called "No Template" which is the first option in the template list. When you select No Template as the region template APEX will only display the content of the region an nothing else (no header, buttons, etc).

[![](http://3.bp.blogspot.com/_33EF80fk9sM/TR3z1SvC0sI/AAAAAAAAD2k/DRv93FxN2UA/s400/no_template.jpg)](http://3.bp.blogspot.com/_33EF80fk9sM/TR3z1SvC0sI/AAAAAAAAD2k/DRv93FxN2UA/s1600/no_template.jpg)
There is a problem with using No Template if you use Dynamic Actions or have any JavaScript code which references the region. The No Template region template does not contain an ID attribute which prevents dynamic actions from binding to the region.

The following steps will create a region that simulates the "No Template" region and includes an ID so it will work with dynamic actions.

- Go to Shared Components
- Click on Templates
- Click the Create button
- Template Type: Region
- Select From Scratch and click Next
- Name: No Template w. ID
&nbsp;&nbsp;Theme: Select your default theme (in most cases you'll only have one option)
&nbsp;&nbsp;Template Class: Reports Region
&nbsp;&nbsp;Click the Create button
- You should be brought back to the Templates page. To filter the list of regions select Region as the Type and click the Go button
- Click on No Template w. ID to modify it
- Put the following HTML in the Definition section<pre class="brush: html;">
<div id="#REGION_STATIC_ID#" #REGION_ATTRIBUTES#>
  #BODY#
</div></pre> - It should look like
[![](http://1.bp.blogspot.com/_33EF80fk9sM/TR34Feko17I/AAAAAAAAD2s/OKH7IxHYETI/s400/no_template_id_template.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TR34Feko17I/AAAAAAAAD2s/OKH7IxHYETI/s1600/no_template_id_template.jpg)- Click the Apply Changes button

If you modify your region that used No Template and change it to the new template that you created, No Template w. ID, it will look the same for the end users with the big difference being that dynamic actions will work when applied against the region.

The following query identifies regions that use the No Template region template which you may want to modify to use the custom No Template w. ID: <pre class="brush: sql;">
SELECT page_id, page_name, region_name, template
  FROM apex_application_page_regions
 WHERE application_id = :app_id
   AND template = 'No Template'</pre><span style="font-style:italic;">Note: I think [Patrick Wolf](http://www.inside-oracle-apex.com/) mentioned that the No Template region template will include an ID in APEX 4.1.</span>
