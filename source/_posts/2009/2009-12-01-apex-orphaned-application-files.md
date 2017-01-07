---
title: APEX Orphaned Application Files
tags:
  - apex
date: 2009-12-01 09:00:00
alias:
---

If you allow end users to upload files to your APEX application you may have a lot of "orphaned" files in apex_application_files and not even realize it.

Orphaned files are files that exist in <span style="font-style:italic;">APEX_APPLICATION_FILES</span> that are not associated with an application. This can happen for several reasons, the most common are:

*   Files uploaded in Shared Components that aren't associated to an application
*   End users uploading files then purposely keep them in <span style="font-style:italic;">APEX_APPLICATION_FILES</span>
*   End users uploading files and an error occurs

You can easily identify orphaned files using the <span style="font-style:italic;">APEX_APPLICATION_FILES</span> view:
<pre class="brush: sql">
SELECT *
  FROM apex_application_files
 WHERE flow_id = 0 -- flow_id is the same as application_id
</pre>
All 3 situations listed above will result in the file uploaded with <span style="font-style:italic;">flow_id = 0</span>. The last 2 points, files uploaded from end users, can result in files that you may no longer need. I don't recommend that you keep uploaded files in the <span style="font-style:italic;">APEX_APPLICATION_FILES</span> view. Instead you should move them immediately to a custom table.

The main problem comes from the third point. When a user uploads a file and a validation fails. In this situation the file is uploaded to <span style="font-style:italic;">APEX_APPLICATION_FILES</span> and then the validation fails. Even though the validation failed, the file still resides in <span style="font-style:italic;">APEX_APPLICATION_FILES</span>. The following screen shot demonstrates this issue.

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SwNsxYj7t_I/AAAAAAAADto/ALhY8Bl_mrU/s400/validation_fail.bmp)](http://2.bp.blogspot.com/_33EF80fk9sM/SwNsxYj7t_I/AAAAAAAADto/ALhY8Bl_mrU/s1600/validation_fail.bmp)
To resolve this issue, I run the following application process which automatically "tags" uploaded files with a flow_id of -1\. By doing so you can run a nightly process to delete any files that have a flow_id of -1.

Application Process: AP_TAG_APEX_FILES
Sequence: -100
Point: On Submit: After Page Submission - Before Computations and Validations
<pre class="brush: sql">
-- AP_TAG_APEX_FILES
BEGIN
   FOR x IN (SELECT v (item_name) item_name_value
               FROM apex_application_page_items
              WHERE application_id = :app_id
                AND page_id = :app_page_id
                AND display_as = 'File Browse...')
   LOOP
      UPDATE apex_application_files aaf
         SET aaf.flow_id = -1
       WHERE aaf.NAME = x.item_name_value;
   END LOOP;
END;
</pre>
