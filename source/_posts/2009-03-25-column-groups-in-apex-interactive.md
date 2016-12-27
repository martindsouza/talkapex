---
title: Column Groups in APEX Interactive Reports
tags:
  - APEX
date: 2009-03-25 21:19:00
alias:
---

<span style="font-style:italic;color:red;">Note: If you are using APEX 4.0 I've developed a plugin for this: [http://www.talkapex.com/2010/12/column-groups-in-apex-40-interactive.html](http://www.talkapex.com/2010/12/column-groups-in-apex-40-interactive.html)</span>

A while ago [Dimitri Gielis](http://dgielis.blogspot.com/) helped us to display column groups in Interactive Reports (IR) in APEX. Please see his [blog posting](http://dgielis.blogspot.com/2008/11/group-headings-in-interactive-report.html) to get a background of how to enable column groups in Interactive Reports.  

[![](http://2.bp.blogspot.com/_33EF80fk9sM/Scr2OYNUyNI/AAAAAAAADn4/GAaBwZhU9tM/s400/01_ir_col_grp.bmp)](http://2.bp.blogspot.com/_33EF80fk9sM/Scr2OYNUyNI/AAAAAAAADn4/GAaBwZhU9tM/s1600-h/01_ir_col_grp.bmp)

After some testing we've made several changes to the code. The updated code fixes a bug that would display multiple group headers. It does not load the group headers via AJAX so it speeds up response time. I've included the updated code below. The example of this is [here.](http://apex.oracle.com/pls/otn/f?p=20195:1000)

<span style="font-style: italic;">Please note you will need [jQuery](http://www.jquery.com) for this example</span>

<span style="font-weight:bold;">Step 1: Application Process</span>
Create an Application Process to load the IR colum group header information
Name: AP_GET_IR_COL_GROUPS
Point: On Load: After Header (page template header)
<pre class="brush: sql;">
DECLARE
  v_sql                         VARCHAR2 (500);
  v_count                       PLS_INTEGER;
BEGIN
  -- Need to ensure at least 1 col group exists
  SELECT COUNT (column_group_id)
  INTO   v_count
  FROM   apex_application_page_ir ir, apex_application_page_ir_col c
  WHERE  ir.application_id = :app_id
  AND    ir.page_id = :app_page_id   
  AND    ir.interactive_report_id = c.interactive_report_id;

-- Need to do join columns to IR rather than the page_id since columns that are added after IR was created need have a null page_id
  v_sql                      := '
SELECT c.column_alias,
       c.report_label,
       c.column_group
FROM   apex_application_page_ir_col c,
       apex_application_page_ir i
WHERE  i.application_id = :app_id
AND    i.page_id = :app_page_id
AND    i.interactive_report_id = c.interactive_report_id
AND    c.column_group IS NOT NULL';
  HTP.p ('<script type="text/javascript">');

  -- Only insert data if IR exists for this page
  IF v_count > 0 THEN
    -- Create JSON object. Note need to use htp.prn so line feed isn't sent
    HTP.prn ('var gPageIRColGrps = $u_eval(''(');
    apex_util.json_from_sql (v_sql);
    HTP.prn (')'');');
  ELSE
    HTP.p ('var gPageIRColGrp = '''';');
  END IF;

  HTP.p ('</script>');
END;
</pre>
<span style="font-weight:bold;">Step 2: Javascript Code</span>
You can put this on Page 0 or just directly on the page with the Interactive Report
<pre class="brush: js;">
dispIRColGrpHeader=function(){
  if(typeof(gPageIRColGrps) != "undefined"){
    // retrieve the Interactive report table
    var vTbl = $('.apexir_WORKSHEET_DATA');

    // change the look and feel of the IR table 
    $(vTbl).attr("border","1");

    // Prevent Duplicate rows
    $('#irColGrpRow').remove();

    // Add the Column Group row
    $(vTbl[0].rows[0]).before('&lt;tr id="irColGrpRow"&gt;&lt;/tr&gt;');

    var vPrevColGrp = '';
    var vColGrpExists = false;
    var vColSpan = 1;
    // Loop over the row headers and see if we need to add a column group.
    for (var i = 0; i < $(vTbl[0].rows[1].cells).length; i++){
      // For IR, the column headers have divs with id of apexir
      vColId = '';
      // Only set the col ID if it exists (needed for IR row_id icon)
      if (typeof($('.apexir_WORKSHEET_DATA tr:eq(1) th:eq(' + i + ') div').attr('id')) != "undefined")
        vColId = $('.apexir_WORKSHEET_DATA tr:eq(1) th:eq(' + i + ') div').attr('id').replace(/apexir_/, '').toUpperCase();
      var vFoundColGrp = false; // This column has an associated column grp
      var vColGrp = ''; // Current Column group

      // Find the ID in the IR Groups global variable (genereated in AP)
      for (var j = 0; j < gPageIRColGrps.row.length; j ++ ){
        if (gPageIRColGrps.row[j].COLUMN_ALIAS.toUpperCase() == vColId) {
          vFoundColGrp = true;
          vColGrpExists = true;
          vColGrp = gPageIRColGrps.row[j].COLUMN_GROUP;
          break;
        }//if
      }// For IR Col Groups

      // Only print the col group header for the previous entry. This allows us to set the col span for similar groups
      // Have to do it this way to support IE  (otherwise we could look at the previous entry and update it's col span

      // If the current 
      if (vColGrp.length > 0 && vColGrp == vPrevColGrp){
        // Don't display the 
        vColSpan = vColSpan + 1;
      }
      else if(i > 0) {
        // Display the previous item
        $('#irColGrpRow').append('&lt;th colspan="' + vColSpan + '"&gt;' + vPrevColGrp + '&lt;/th&gt;');
        vColSpan = 1;
      }

      // If this is the last item then display it
      if (i == $(vTbl[0].rows[1].cells).length-1) {
        $('#irColGrpRow').append('&lt;th colspan="' + vColSpan + '"&gt;' + vColGrp + '&lt;/th&gt;');
      }

      vPrevColGrp = vColGrp;
    }// For each column being displayed

    // Remove the col group heading if no column groups:
    if (!vColGrpExists)
        $('#irColGrpRow').remove();
  } // Column groups exist

} // dispIRColGrpHeader

// Once the page is finsihed loading run the following (which will activate the column grouping display).
$(document).ready(function(){

// If page has an IR then set up for column grouping
  if ($('.apexir_WORKSHEET_DATA').length > 0) {

    function dispIRColGroups(){
      // Set the class for div hover since can't do in IE
      $('table.apexir_WORKSHEET_DATA th div').mouseover(function(){
        $(this).addClass('apexir_WORKSHEET_DATA_th_div_hover');
      });
      $('table.apexir_WORKSHEET_DATA th div').mouseout(function(){
        $(this).removeClass('apexir_WORKSHEET_DATA_th_div_hover');
      });  

     dispIRColGrpHeader();
     // This time out is required since after the report is refreshed via AJAX, need to reattach the l_LastFunction command
     setTimeout(
       function(){
        gReport.l_LastFunction = function(){dispIRColGroups();};
       },
       1000
      );
    }

    gReport = new apex.worksheet.ws('');
    gReport.l_LastFunction = function(){dispIRColGroups();}
    dispIRColGroups();
  }//if
});    
</pre>