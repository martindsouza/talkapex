---
title: Running Custom Code for Tabular Forms (Part 1)
tags:
  - apex
date: 2014-03-31 08:34:00
alias:
---

_**Note: **An updated version of this article is now available which covers how to [run custom code for a manual Tabular Form in APEX 5](http://www.talkapex.com/2015/09/custom-code-for-tabular-forms-part-1.html). There is also a new article which covers [how to modify data from multiple tables in the same Tabular Form](http://www.talkapex.com/2015/09/custom-code-for-tabular-forms-part-2.html)._

One of my biggest pet peeves with Tabular Forms in APEX is that it would only run basic (Insert, Update, Delete) DML functions against a table. This works really well for basic situations but more often than not data must be processed by a procedure to handle all the business logic. For this reason, I've avoided Tabular Forms for a very long time.

Last week at [OGh APEX World](https://www.ogh.nl/page.aspx?event=213), [Dimitri Gielis](http://dgielis.blogspot.ca/) showed how you can run your own procedure against a tabular form. Here's how to do it:

First, create a Tabular Form using the standard wizard. This will create the standard validations and processes for the page. Their will be two Page Processing processes as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-7N6stG7eAhU/Uzl18x7uIfI/AAAAAAAAEpc/6lokPskB2JU/s1600/Snip20140331_1.png)](http://3.bp.blogspot.com/-7N6stG7eAhU/Uzl18x7uIfI/AAAAAAAAEpc/6lokPskB2JU/s1600/Snip20140331_1.png)</div>These processes will automatically handle any of the data changes that a user makes in the Tabular Form. For now you can go ahead and remove each process as we'll use a custom procedure to process the data instead.

Next, create a new process. The important part comes when creating the process; be sure to select the Tabular Form option (as shown below).

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-CGeyt9fORHw/Uzl61_AZ9gI/AAAAAAAAEps/1rJqaEGjEHM/s1600/Snip20140331_2.png)](http://2.bp.blogspot.com/-CGeyt9fORHw/Uzl61_AZ9gI/AAAAAAAAEps/1rJqaEGjEHM/s1600/Snip20140331_2.png)</div>In your PL/SQL block, you can now reference each of the columns using their column names (example `:SAL` and `:ENAME`). What's even better is that APEX will only run the code against rows that have changed which can save a lot of processing time. For example, if only two rows in a 15 row table were changed the code will be executed twice.

In the next article I'll show how to expand this functionality beyond a base table and use this new technique to modify any data set.
