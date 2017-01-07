---
title: Log APEX Interactive Report Search Filter
tags:
  - apex
date: 2009-04-01 07:22:00
alias:
---

A few days ago someone posted a question on the forums [http://forums.oracle.com/forums/message.jspa?messageID=3365707](http://forums.oracle.com/forums/message.jspa?messageID=3365707) asking how to log the search filter in an IR. Here's how to do it. A working example is available [here.](http://apex.oracle.com/pls/otn/f?p=20195:1100)

<span style="font-style:italic;">Please note you will need [jQuery](http://www.jquery.com) for this example</span>

<span style="font-weight:bold;">Step 1: Create Log Table</span>

<pre class="brush: sql">
  CREATE TABLE tir_search_filter_log(
    search_filter VARCHAR2(255) NULL,
    search_date DATE DEFAULT SYSDATE NOT NULL,
    username VARCHAR2(255) NOT NULL);
</pre>
<span style="font-weight:bold;">Step 2: Create Application Process</span>

Create an On Demand Application Process. Call it: AP_LOG_SEARCH_FILTER
<pre class="brush: sql;">
  BEGIN
    INSERT INTO tir_search_filter_log
                (search_filter,
                 search_date,
                 username
                )
    VALUES      (apex_application.g_x01,
                 SYSDATE,
                 :app_user
                );
  END;
</pre>
<span style="font-weight:bold;">Step 3: Create Interactive Report</span>

Create a IR Page
<pre class="brush: sql;">
  SELECT *
  FROM   emp
</pre>
<span style="font-weight:bold;">Step 3: Javascript</span>

Create a HTML region and add the following javascript code:
<pre class="brush: js;">
  // Function to call our Application Process to log the search
  function fLogSearch(){
    var get = new htmldb_Get(null,$v('pFlowId'),'APPLICATION_PROCESS=AP_LOG_SEARCH_FILTER',$v('pFlowStepId'));    
    get.addParam('x01',$v('apexir_SEARCH')); // IR Search Filter Value
    gReturn = get.get();  // Call AP Log Search Filter to insert log
    // Call APEX IR Search
    gReport.search('SEARCH');
  }

  // Run the following once the document is ready
  $(document).ready(function(){  
    // -- Handle Go Button --
    // Unbind all events. Important for order of execution
    $('input[type="button"][value="Go"]').attr('onclick',''); //unbind click event
    // Rebind events
    $('input[type="button"][value="Go"]').click(function(){fLogSearch()});

    // -- Handle "Enter" in input field --
    $('#apexir_SEARCH').attr('onkeyup',''); //unbind onkeyup event
    // Rebind Events
    $('#apexir_SEARCH').keyup(function(event){($f_Enter(event))?fLogSearch():null;});
  });
</pre>
