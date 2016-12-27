---
title: APEX Report Download Logger
tags:
  - APEX
  - ORACLE APEX
date: 2010-07-06 09:00:00
alias:
---

[![](http://1.bp.blogspot.com/_33EF80fk9sM/TDJYewihZFI/AAAAAAAADxQ/J6hgRy4E1wI/s320/1119379849_14860651001_History-Ax-Men-Logger-Advice-SF.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/TDJYewihZFI/AAAAAAAADxQ/J6hgRy4E1wI/s1600/1119379849_14860651001_History-Ax-Men-Logger-Advice-SF.jpg)
<span style="font-style:italic;">This solution was demoed at the  [ODTUG](http://www.odtugkaleidoscope.com/) APEX Open Mic night</span>

I developed a reporting application in APEX. The goal of the application is to help turn "data into information". Users should be able to make business decisions based on the reports and charts rather than exporting the report data and using spreadsheets to analyze it.

I've been a bit concerned that the application isn't achieving it's initial goal and that the reports are just used as data dumps. To validate this hypothesis I needed a way to track how many times a report was downloaded and compare it to the number of times it was viewed.

I can find out how many times a report/page was displayed by looking at the APEX logs: 
<pre class="brush: sql;">
SELECT *
  FROM apex_workspace_activity_log
 WHERE application_id = :app_id
   AND page_id = :app_page_id
</pre>
<span style="font-style:italic;">Note: this approach is not 100% accurate since it only logs the page and not the individual regions, however for this purpose it should do. I have tinkered with "Region Logging" and may write about it soon.</span>

I still needed a way to track report downloads. Here's how I did it. A working demo can be found here: [http://apex.oracle.com/pls/apex/f?p=20195:3120](http://apex.oracle.com/pls/apex/f?p=20195:3120)

- Create Download Log table:
<pre class="brush: sql;">
CREATE TABLE TAPEX_WORKSPLACE_REPORT_DL_LOG
(
  WORKSPACE                  VARCHAR2 (255 BYTE) NOT NULL,
  WORKSPACE_ID               NUMBER NOT NULL,
  APPLICATION_ID             NUMBER NOT NULL,
  APPLICATION_NAME           VARCHAR2 (255 BYTE) NOT NULL,
  APPLICATION_SCHEMA_OWNER   VARCHAR2 (30 BYTE) NOT NULL,
  PAGE_ID                    NUMBER NOT NULL,
  PAGE_NAME                  VARCHAR2 (255 BYTE) NOT NULL,
  REGION_ID                  NUMBER NOT NULL,
  REGION_NAME                VARCHAR2 (255 BYTE) NOT NULL,
  VIEW_DATE                  DATE NOT NULL,
  APEX_USER                  VARCHAR2 (255 BYTE) NOT NULL,
  APEX_SESSION_ID            NUMBER NOT NULL
);
</pre>
- Create procedure to store APEX download record
<pre class="brush: sql;">
/**
 * Insert Download Report Record
 * @param p_region_id Report Region ID
 */

CREATE OR REPLACE PROCEDURE sp_log_apex_dl_report (
  p_region_id IN apex_application_page_regions.region_id%TYPE)
AS
BEGIN
  -- Insert into report download log table
  INSERT INTO tapex_worksplace_report_dl_log (WORKSPACE,
                                              WORKSPACE_ID,
                                              APPLICATION_ID,
                                              APPLICATION_NAME,
                                              APPLICATION_SCHEMA_OWNER,
                                              PAGE_ID,
                                              PAGE_NAME,
                                              REGION_ID,
                                              REGION_NAME,
                                              VIEW_DATE,
                                              APEX_USER,
                                              APEX_SESSION_ID)
    SELECT aapr.workspace,
           AA.WORKSPACE_ID,
           aapr.application_id,
           aapr.application_name,
           aa.owner,
           aapr.page_id,
           AAPr.PAGE_NAME,
           aapr.region_id,
           aapr.region_name,
           SYSDATE,
           v ('APP_USER'),
           v ('APP_SESSION')
      FROM APEX_APPLICATION_PAGE_regionS aapr, APEX_APPLICATIONS aa
     WHERE aapr.application_id = v ('APP_ID')
       AND AAPr.PAGE_ID = v ('APP_PAGE_ID')
       AND aapr.region_id = p_region_id
       AND aapr.application_id = aa.application_id;
END sp_log_apex_dl_report;
</pre>
- Trigger the above procedure every time a report is downloaded:

Since Standard Reports (STD) and Interactive Reports (IR) download functionalities are handled differently I can't use an Applicatiobn Process to trigger the logging function. Instead, I'm going to leverage the VPD section in APEX. For those of you new to APEX, the Virtual Private Database (VPD) section in APEX is a section in APEX where you can put a block of PL/SQL code. This code gets run right at the beginning of the page, after the APP_USER is defined. The label of VPD is a bit misleading since the code doesn't have to do any VPD tasks. <span style="font-style:italic;">Initially I had planned to handle IRs and STD Reports using a different method but thanks to the guys at Purdue Pharma for reminding me that I can leverage the VPD section in APEX.</span> 

- Shared Components
  - Security Attributes
    - Virtual Private Database

<pre class="brush: sql;">
DECLARE
  v_region_id   APEX_APPLICATION_PAGE_REGIONS.REGION_ID%TYPE;
BEGIN
  -- If CSV/HTMLD is for IR (does not factor in email IRs). FLOW_EXCEL_OUTPUT is for Standard Report
  IF :request IN ('CSV', 'HTMLD')
  OR  :request LIKE ('FLOW_EXCEL_OUTPUT%') THEN
    -- Check for Standard Report
    IF :request LIKE ('FLOW_EXCEL_OUTPUT%') THEN
      v_region_id := REGEXP_SUBSTR (:request, '[[:digit:]]+');
    ELSE
      -- Interactive Report
      SELECT region_id
        INTO v_region_id
        FROM APEX_APPLICATION_PAGE_ir
       WHERE application_id = :app_id
         AND page_id = :app_page_id;
    END IF;

    sp_log_apex_dl_report (p_region_id => v_region_id);
  END IF;
END;
</pre>
- Comparison report:

Here's the query that I used to compare the downloads to the report views
<pre class="brush: sql;">
SELECT AR.APPLICATION_ID,
       ar.application_name,
       ar.page_id,
       ar.page_name,
       ar.region_id,
       ar.region_name,
       tdl.downloads,
       al.page_views
  FROM APEX_APPLICATION_PAGE_REGIONS ar,
       (SELECT tdl.application_id,
               tdl.page_id,
               tdl.region_id,
               COUNT (tdl.application_id) downloads
          FROM TAPEX_WORKSPLACE_REPORT_DL_LOG tdl
        GROUP BY tdl.application_id, tdl.page_id, tdl.region_id) tdl,
       (SELECT al.application_id, al.page_id, COUNT (al.application_id) page_views
          FROM apex_workspace_activity_log al
        GROUP BY al.application_id, al.page_id) al
 WHERE AR.APPLICATION_ID = :app_id
   AND ar.source_type_code IN ('SQL_QUERY', 'DYNAMIC_QUERY')
   -- Downloads
   AND ar.region_id = tdl.region_id(+)
   -- Page Views
   AND ar.application_id = al.application_id(+)
   AND ar.page_id = al.page_id(+)
</pre>