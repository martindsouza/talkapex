---
title: APEX_JSON.PARSE Issue with CLOBs and 11g
tags:
  - APEX 5
date: 2015-05-12 10:07:00
alias:
---

_Update: this issue is documented on the [APEX 5 Known Issues page](http://www.oracle.com/technetwork/developer-tools/apex/application-express/apex-50-known-issues-2504535.html) (bug&nbsp;20974582) and has a patch for users with access to MOS (bug&nbsp;20931298)._

APEX 5.0 comes with some new APIs including [APEX_JSON](https://docs.oracle.com/cd/E59726_01/doc.50/e39149/apex_json.htm#AEAPI29635) which manages JSON files.

When using APEX_JSON to consume a JSON file, the process is fairly straight forward:
- Parse: This parses the JSON and stores it in a record type.
- Read values in the JSON object by providing the path to each name/value pair.

The APEX_JSON documentation has some great examples which covers the above process in more details.

There is one "bug" that I found with <span style="font-family: Courier New, Courier, monospace;">[APEX_JSON.PARSE](https://docs.oracle.com/cd/E59726_01/doc.50/e39149/apex_json.htm#AEAPI29747)</span> when passing in a CLOB that has more than 8191 characters. &nbsp;The reason is <span style="font-family: Courier New, Courier, monospace;">APEX_JSON.PARSE</span> uses <span style="font-family: Courier New, Courier, monospace;">DBMS_LOB.SUBSTR</span> behind the scenes which has a known bug in 11g R1, 11g R2, and 11g XE. In 12c onwards, this is not an issue. An example of the error that is raised is:

<pre class="brush: sql;">ORA-20987: Error at line 626, col19: Expected ":", seeing "&lt;varchar2&gt;"

ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 292
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 560
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 756
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 774
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 624
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 719
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 615
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 758
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 774
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 811
ORA-06512: at "APEX_050000.WWV_FLOW_JSON", line 887
ORA-06512: at line 24
</pre>
The <span style="font-family: Courier New, Courier, monospace;">APEX_JSON.PARSE</span> procedure currently has two supported overloaded methods. One which takes in a varchar2, and the other which takes in a CLOB. Thankfully there's a third (undocumented) method which takes in a table of varchar2\. _Note: in future versions of APEX, this may be a supported procedure._

Here's a sample snippet on how to convert your CLOB to a varchar2 table and then call <span style="font-family: Courier New, Courier, monospace;">APEX_JSON.PARSE</span>:
<pre class="brush: sql;">declare
  l_clob clob;
  l_json apex_json.t_values;

  l_temp varchar2(1000);

  l_clob_tab apex_application_global.vc_arr2;

begin

  l_clob := 'load json data here';

  -- Convert clobtotable
  declare
    c_max_vc2_size pls_integer := 8100; -- Bug with dbms_lob.substr 8191

    l_offset pls_integer := 1;
    l_clob_length pls_integer;
  begin
    l_clob_length := dbms_lob.getlength(l_clob);

    while l_offset &lt;= l_clob_length loop
      l_clob_tab(l_clob_tab.count + 1) :=
        dbms_lob.substr (
         lob_loc =&gt; l_clob,
         amount =&gt; least(c_max_vc2_size, l_clob_length - l_offset +1 ),
         offset =&gt; l_offset);

      l_offset := l_offset + c_max_vc2_size;
    end loop;
  end;

  -- Parse clob as JSON
  apex_json.parse(
    p_values =&gt; l_json,
    p_source =&gt; l_clob_tab,
    p_strict =&gt; true);

  -- You can now use the JSON object for l_json
end;
</pre>Note: the code to convert the CLOB to the table will be available soon as part of the new OraOpenSource [OOS_UTILS project](http://www.oraopensource.com/blog/2015/4/23/oos-utilities-package).

_Thanks to [Christian Neumüller](https://chrisonoracle.wordpress.com/)&nbsp;for helping resolve this issue._
<div>
</div>