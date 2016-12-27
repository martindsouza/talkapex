---
title: How to Send/Upload a CLOB from the Browser to APEX via AJAX
tags:
  - APEX
date: 2012-08-28 09:39:00
alias:
---

Today [Alistair Lang](https://twitter.com/AliUK12) asked on Twitter "_how do I pass in a CLOB to an on-demand process using AJAX in APEX?_". I had this same questions a few months ago when I was working on uploading files using AJAX into APEX.

It turns out you can't use the standard <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">addParam</span> APEX JavaScript method (hopefully this will change in 4.2). Instead you need call a different function which will store the CLOB into a special APEX collection then process the CLOB from that collection. Here's a breakdown of what needs to happen:

- Send the CLOB from the browser to APEX. It will be stored in the <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">CLOB001</span> column in the collection "CLOB_CONTENT".
- Once the CLOB is sent to APEX call your On Demand process (i.e. AJAX request) to run some PL/SQL code. This PL/SQL code will need to retrieve the CLOB value from the collection

Here's an example

**On Demand Process**: On your page create an On Demand process called "MY_PROCESS". In the code enter the following:
<pre class="brush: sql; highlight: []">
DECLARE
  l_clob CLOB;
BEGIN
  SELECT clob001 
  INTO l_clob
  FROM apex_collections 
  WHERE collection_name = 'CLOB_CONTENT';

  -- Now you can process the CLOB using l_clob
END;
</pre>**JavaScript Code**: This can be stored either in a Dynamic Action or custom JS code:
<pre class="brush: js; highlight: []">
/**
 * Code to run once the upload is done
 * 
 * Clob is now accessible in the apex_collections view:
 * SELECT collection_name, seq_id, clob001 FROM apex_collections 
 * WHERE collection_name = 'CLOB_CONTENT';
 *  - Note: The collection name "CLOB_CONTENT" is not modifiable
 * 
 * Use this function to make an AJAX request to trigger 
 * an On Demand Process (i.e. run some PL/SQL code)
 */
function clubUploadDone(){
  var get = new htmldb_Get(null,$v('pFlowId'),'APPLICATION_PROCESS=MY_PROCESS',$v('pFlowStepId'));
  //Optional: pass some additional values get.addParam('x01','some data');
  gReturn = get.get();
}

/**
 * Send clob to APEX (will be stored in the apex_collection "CLOB_CONTENT"
 */
var clobObj = new apex.ajax.clob(
  //Callback funciton. only process CLOB once it's finished uploading to APEX
  function(p){
    if (p.readyState == 4){
      clubUploadDone();
    }
  });

clobObj._set('Put clob content here'); //Sends the data to Oracle/APEX collection

</pre>It's important to note that there is only one area where the CLOB data is stored so each time you send a new CLOB it will erase the older value in the collection. If you're sending multiple CLOBS sequentially you need to handle it accordingly. A good example of this is if you're uploading multiple files via a drag &amp; drop interface.