---
title: Derived Columns in APEX Standard Reports
tags:
  - apex
date: 2014-01-06 07:00:00
alias:
---

A while back I wrote an [article](http://www.talkapex.com/2010/06/special-columns-for-apex-standard.html) about a feature in APEX's standard reports called Derived Columns. Despite using APEX for a very long time I had never really noticed it or used it before. Someone asked for some more information on derived columns, so this is a follow up article with a bit more detail.

Derived columns are place holders for an additional column in the report. The column can reference any of the query's columns but has some limitations such as not declaratively supporting links or being about to compute a value. It's most useful when you need an additional column to be displayed in a specific HTML format.

In the past when I wanted an additional column for display purposes I would just add it in the query as `..., null my_additional_column ... ` and then modify the column's HTML Expression value. Now I can just a derived column and modify its HTML Expression.

To add a derived column, go to the Report Attributes then click Add Derived Column on the right hand side as shown below:

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-JR1q3oVHd0k/Usg3rAIhNVI/AAAAAAAAEjg/qRJjCwOLt40/s200/Report-Attributes.png)](http://4.bp.blogspot.com/-JR1q3oVHd0k/Usg3rAIhNVI/AAAAAAAAEjg/qRJjCwOLt40/s1600/Report-Attributes.png)</div>

After clicking the above link a new column will appear in the list of columns.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-7ibOy2BZhWs/Usg4TWtxb6I/AAAAAAAAEjo/P30Eb2rdVe0/s640/Column-Attributes.png)](http://3.bp.blogspot.com/-7ibOy2BZhWs/Usg4TWtxb6I/AAAAAAAAEjo/P30Eb2rdVe0/s1600/Column-Attributes.png)</div>

You'll need to edit the newly added derived column and add something in the Column Formatting > HTML Expression field for values to appear. **_Tip_**: to reference columns in the HTML Expression field use the `#COLUMN_NAME#` notation.
