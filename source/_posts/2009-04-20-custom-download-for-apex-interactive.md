---
title: Custom Download for APEX Interactive Reports
tags:
  - apex
date: 2009-04-20 07:44:00
alias:
---

A few months ago I needed to modify the downloaded Interactive Report (IR) to CSV function so that we could include group headings, report description, and report parameters (please see [Column Groups in APEX Interactive Reports](http://apex-smb.blogspot.com/2009/03/column-groups-in-apex-interactive.html) on how to display column groups in IR). [Denes Kubicek](http://deneskubicek.blogspot.com/) has a package to download regular reports in Excel format, however it does not return the query that the user is currently viewing. This is really important since users may add columns, change the ordering of columns, etc. When users download a report it should reflect what is currently being displayed on the screen.

After digging through the APEX packages I was able to write some code to generate my own custom download function for IRs. Since the code is fairly long you'll need to get a copy of the code [here](http://apex.oracle.com/pls/otn/f?p=20195:1300) (please save the file as pkg_apex_report.zip). <span style="font-weight:bold; color: red">Note: This code is NOT ready for a production environment!</span>. It's also important to note that some of the grant privileges could have <span style="font-weight:bold; color: red">security implications</span> and is not recommend to be run for public applications. If you need to use these grants in a production instance I suggest creating a special "APEX" schema and write some custom wrapper functions. Since grants are required from the APEX schema I can't post a working example of this application on [apex.oracle.com](http://apex.oracle.com).

Now that I'm done with my disclaimers here's how to customize IR downloads (don't forget to download the code). <span style="font-style:italic;">Please note that the schema I developed in this was called "giffy".</span>

<span style="font-weight:bold;">1- Grant Privileges</span>

<pre class="brush: sql">
  -- Change "apex_030200" to the version of APEX that you are using
  -- Change "giffy" to your schema
  GRANT EXECUTE ON apex_030200.wwv_flow_worksheet_standard TO giffy;
  CREATE OR REPLACE SYNONYM giffy.wwv_flow_worksheet_standard FOR apex_030200.wwv_flow_worksheet_standard

  GRANT EXECUTE ON apex_030200.wwv_flow_conditions TO giffy;
  CREATE OR REPLACE SYNONYM giffy.wwv_flow_conditions FOR apex_030200.wwv_flow_conditions;

  GRANT EXECUTE ON apex_030200.wwv_flow_worksheet TO giffy;
  CREATE OR REPLACE SYNONYM giffy.wwv_flow_worksheet FOR apex_030200.wwv_flow_worksheet;

  GRANT EXECUTE ON apex_030200.wwv_flow_render_query TO giffy;
  CREATE OR REPLACE SYNONYM giffy.wwv_flow_render_query FOR apex_030200.wwv_flow_render_query;
</pre>

<span style="font-weight:bold;">2- Create Package apex_report</span>

Download code [here](http://apex.oracle.com/pls/otn/f?p=20195:1300) (save the file as pkg_apex_report.zip)
In SQL*Plus run:
<pre class="brush: sql">
  @pkg_apex_report.pks
  @pkg_apex_report.pkb
</pre>

<span style="font-weight:bold;">3- Create Interactive Report region</span>
<pre class="brush: sql">
  SELECT *
    FROM emp
</pre>

<span style="font-weight:bold;">4- Create "Download Page"</span>
- Create a new HTML Page.
- Create a page item P3_BASE_REPORT_ID
- Create a page item P3_PAGE_ID

- Create a PL/SQL Process
  - Point: On Load Before Header
  - PL/SQL:

<pre class="brush: sql">
  DECLARE
    v_region_id apex_application_page_regions.region_id%TYPE;
    v_base_report_id apex_application_page_ir_rpt.base_report_id%TYPE;
    v_page_id apex_application_page_ir_rpt.page_id%TYPE;
  BEGIN
    v_base_report_id := :p3_base_report_id;
    v_page_id := :p3_page_id;

    SELECT ir.region_id
      INTO v_region_id
      FROM apex_application_page_ir_rpt irr,
           apex_application_page_ir ir
     WHERE irr.application_id = :app_id
       AND irr.page_id = v_page_id
       AND irr.session_id = :app_session
       AND irr.base_report_id = v_base_report_id
       AND irr.interactive_report_id = ir.interactive_report_id;

    pkg_apex_report.sp_download_report (p_page_id            => v_page_id,
                                        p_region_id          => v_region_id,
                                        p_base_report_id     => v_base_report_id,
                                        p_format_typ         => 'CSV'
                                       );
  END;
</pre>

<span style="font-weight:bold;">5- Alter Interactive Report Download Link</span>
<span style="font-style:italic;">Please see [APEX Interactive Report - Download with One Click](http://apex-smb.blogspot.com/2009/03/interactive-report-download-with-one.html) for more information about this code</span>. You'll need [jQuery](http://www.jquery.com) for this.

<pre class="brush: js">
//Note: "3" is my download page
$(document).ready(function() {
  $('.dhtmlSubMenuN[title="Download"]').attr('href','f?p=' + $v('pFlowId') + ':3:' + $v('pInstance') + '::NO:3:P3_PAGE_ID,P3_BASE_REPORT_ID:' + $v('pFlowStepId') + ',' + $v('apexir_REPORT_ID'));  
});
</pre>

At this point you should be able to run your interactive report and download the custom CSV file. You'll notice that there's a block of HTML that appears at the top of the CSV file. This is caused by the call (wwv_flow_worksheet.get_worksheet_report_query) to get the SQL the user is currently looking at. I don't have a fix or a work around (if you know of one please post in comment section)

Please post any bugs, suggestions, or updates to the code as I'd like to create a production version for this code.
