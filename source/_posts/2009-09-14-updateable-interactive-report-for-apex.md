---
title: Updateable Interactive Report for APEX
tags:
  - APEX
  - ORACLE
date: 2009-09-14 09:00:00
alias:
---

A colleague had a requirement where he had over 10,000 rows of data which had to be updateable. Using Interactive Reports (IR) was the preferred approach as it would allow users to filter the data to modify the rows they wanted to before submitting the page. Tabular forms wouldn't work since the page would be to large. This is the solution that I proposed to make "Updateable IRs".

The following solution will work with IRs and standard reports with pagination. If the users applies filters or paginates to another set of data, the changes they make will remain in the collection. This is using a similar technique that I wrote about for [APEX Report with checkboxes (advanced)](http://apex-smb.blogspot.com/2009/01/apex-report-with-checkboxes-advanced.html). To summarize this process:

*   Store the current query data into a collection as well as the md5 checksum.
*   Build an IR against the collection and use APEX_ITEMs to display input fields.*   When a user changes a field, we submit that change to the collection.
*   Once the user is done with their changes you'll need to process the collection as required. In the last step in this example I have a query that will help identify changed rows.
You can do a lot with this approach but if you don't have an urgent need I'd suggest holding off until APEX 4.0\. They're some security issues that would need to be addressed before launching this code in a public application. I didn't include the security updates in this example since I did not want to lose scope of the base functionality. Updating the code to make it secure shouldn't be too difficult.

Here's the link to the demo: [http://apex.oracle.com/pls/otn/f?p=20195:2300](http://apex.oracle.com/pls/otn/f?p=20195:2300)

[![](http://1.bp.blogspot.com/_33EF80fk9sM/SqxaSe21azI/AAAAAAAADqU/xsH4IhHxhwg/s400/updateable_ir.jpg)](http://1.bp.blogspot.com/_33EF80fk9sM/SqxaSe21azI/AAAAAAAADqU/xsH4IhHxhwg/s1600-h/updateable_ir.jpg)

<span style="font-weight: bold;">- Create IR Report Region</span>
<span style="font-style: italic;">Note: You can use this for regular reports with pagination as well</span>
<pre class="brush: sql">
SELECT e.empno,
    apex_item.text (2,
                    ac.c002,
                    NULL,
                    NULL,
                       'class="updateableIR" seqid="'
                    || ac.seq_id
                    || '" attrNum="2"'
                   ) ename,
    apex_item.text (3,
                    ac.c003,
                    NULL,
                    NULL,
                       'class="updateableIR" seqid="'
                    || ac.seq_id
                    || '" attrNum="3"'
                   ) job,
    apex_item.select_list_from_query
       (4,
        ac.c004,
           'select E.ENAME d, E.EMPNO r from emp e where E.EMPNO != '
        || e.empno,
        'class="updateableIR" seqid="' || ac.seq_id || '" attrNum="4"',
        'YES',
        NULL,
        '- Manager -'
       ) mgr,
    apex_item.text (5,
                    ac.c005,
                    NULL,
                    NULL,
                       'class="updateableIR" seqid="'
                    || ac.seq_id
                    || '" attrNum="5"'
                   ) hiredate,
    apex_item.text (6,
                    ac.c006,
                    NULL,
                    NULL,
                       'class="updateableIR" seqid="'
                    || ac.seq_id
                    || '" attrNum="6"'
                   ) comm,
    apex_item.select_list_from_query
                       (7,
                        ac.c007,
                        'SELECT d.dname d, d.deptno r FROM dept d',
                           'class="updateableIR" seqid="'
                        || ac.seq_id
                        || '" attrNum="7"',
                        'YES',
                        NULL,
                        '- Department -'
                       ) deptno
FROM apex_collections ac, emp e
WHERE ac.collection_name = :p2300_collection_name AND ac.c001 = e.empno
</pre>

<span style="font-weight: bold;">- Create Region Items</span>
- Hidden &amp; Protected: P2300_COLLECTION_NAME

<span style="font-weight: bold;">- Create Page Computation</span>
<span style="font-style: italic;">Note: This is just to set the collection name. You can call it whatever you want</span>

Item: P2300_COLLECTION_NAME
Computation Point: Before Header
Computation Type: Static Assignment
Computation: P2300_IR_COLLECTION

<span style="font-weight: bold;">- Create a Page Process  (PL/SQL)</span>

Name: Load Collection
Process Point: On Lead - Before Header
<pre class="brush: sql">
-- This creates the collection if it isn't created yet.
DECLARE
v_collection_name             apex_collections.collection_name%TYPE
                                                 := :p2300_collection_name;
v_reset_flag                  VARCHAR2 (1) := 'N';
BEGIN
-- Create collection if it does not exist or reset collection required
IF    apex_collection.collection_exists
                                   (p_collection_name            => v_collection_name) =
                                                                     FALSE
  OR v_reset_flag = 'Y'
THEN
 apex_collection.create_collection_from_query
   (p_collection_name            => :p2300_collection_name,
    p_query                      => q'! SELECT empno,
    ename,
    job,
    mgr,
    TO_DATE (hiredate, 'DD-MON-YYYY'),
    comm,
    deptno
FROM emp !',
    p_generate_md5               => 'YES'
   );        -- Generated md5 is important to help identify changed columns
END IF;
END;
</pre>

<span style="font-weight: bold;">- Create HTML Region to store JS code:</span>
Note: This uses [jQuery](http://jquery.com/) and [Tyler Muth's](http://tylermuth.wordpress.com) [jApex plugin](http://tylermuth.wordpress.com/2009/08/19/japex-a-jquery-plugin-for-apex/). You'll have to upload the .js files as static files in Shared Components.

<pre class="brush: html">
<script src="#APP_IMAGES#jquery-1.3.2.min.js" type="text/javascript"></script>
<script src="#APP_IMAGES#jquery.jApex.0.9.2.js" type="text/javascript"></script>

<script type="text/javascript">
/** 
 * Updates a cell in a collection
 * @author Martin Giffy D'Souza. http://apex-smb.blogspot.com
 * @param pThis input item that was changed
 *  Must contain the following attributes:
 *  - seqid Seq ID from collection
 *  - attrnum Attribute number (column number)
 * @param pOptions Options
 *  - appProcess: Application process to call
 *  - collectionName: Collection to update
 *  - successFn: Function to execute once completed
 */
function updateCollectionCell(pThis, pOptions){
  var vDefaults = {
    appProcess: 'AP_UPDATE_COLLECTION_CELL',
    collectionName: '',
    successFn: function(){}
  };
  pOptions = jQuery.extend({}, vDefaults, pOptions);  

  // Check that collection name is present
  if (pOptions.collectionName == ''){
    alert('Missing Collection Name');
    return;
  }

  jQuery.jApex.ajax({
    appProcess: pOptions.appProcess,
    success: pOptions.successFn,
    x01: pOptions.collectionName,
    x02: $(pThis).attr('seqid'), //Seq ID
    x03: $(pThis).attr('attrnum'), //Attribute Number (i.e. column number)
    x04: $(pThis).val() // New Value
  });

  return;
}// updateCollectionCell

//Set all updateableIR columns onChange events
$('.updateableIR').live('change',function(){
  updateCollectionCell(this, {
    collectionName: $v('P2300_COLLECTION_NAME'),
    successFn: function(){}
    });
});
</script>
</pre>

<span style="font-weight: bold;">- Create Application Process:</span>
Name: AP_UPDATE_COLLECTION_CELL
Process Point: On Demand

<pre class="brush: sql">
-- AP_UPDATE_COLLECTION_CELL
BEGIN
apex_collection.update_member_attribute
                             (p_collection_name            => apex_application.g_x01,
                              p_seq                        => apex_application.g_x02,
                              p_attr_number                => apex_application.g_x03,
                              p_attr_value                 => apex_application.g_x04
                             );
END;
</pre>

<span style="font-weight: bold;">- Query to see which rows were changed</span>
<pre class="brush: sql">
SELECT ac.collection_name, ac.seq_id, ac.c001, ac.c002, ac.c003, ac.c004,
    ac.c005, ac.c006, ac.c007, ac.md5_original,
    CASE
      WHEN apex_collection.get_member_md5
                                       (:p2300_collection_name,
                                        ac.seq_id) =
                                                ac.md5_original
        THEN 'NO'
      ELSE 'YES'
    END row_change
FROM apex_collections ac
WHERE collection_name = :p2300_collection_name
</pre>