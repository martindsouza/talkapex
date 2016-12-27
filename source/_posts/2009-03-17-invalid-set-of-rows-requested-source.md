---
title: Invalid set of rows requested, the source data of the report has been modified. - Explanation
date: 2009-03-17 22:18:00
tags: apex
alias:
---

When I first started using APEX I occasionally got the following pagination error message: `Invalid set of rows requested, the source data of the report has been modified. reset pagination`

At first I had no clue what was causing the error but after a while I finally figured it out. The best way to describe the error is to see exactly what is happening. Full example is here

**Create a report**

```sql
SELECT *
  FROM emp
 WHERE UPPER (ename) LIKE '%' || UPPER (:P800_LETTER) || '%'
```


When creating the report, change the number of rows from `15` to `5` for this example. **This is important for this demonstration!**

Now create a page item called `P800_LETTER` and a submit button who submits and redirect to `P800`.

If things went well you'd have a report that looks like this:

![](http://1.bp.blogspot.com/_33EF80fk9sM/ScB3T-xfUyI/AAAAAAAADnA/RKxPha9YmBk/s400/01_reset_pagination.bmp)

To illustrate the problem click on the `Next` pagination link so that we are seeing rows `6~10`.
Now, in the text box enter the letter `I` (this will restrict to 4 rows) and click submit. You should see the following error message:

![](http://1.bp.blogspot.com/_33EF80fk9sM/ScB30k-IF-I/AAAAAAAADnI/BSuRiX4RT0I/s400/02_reset_pagination.bmp)


To summarize what just happened, you initially requested a report with 14 rows and told APEX to display 5 at a time. When you clicked the `Next` pagination button you told APEX for the report you're currently looking at display rows `6~10`. So now, if you were to refresh the page APEX would continue to show rows `6~10`. When you filtered the report, by restricting to employees with the letter `I` in their name, you get a result with only 4 rows however APEX is still trying to display rows `6~10`. I hope you now see the problem with this.

So now that we've demonstrated the problem, how do you fix it? Well there's several ways. To solve our current issue, on `P800` in the submit branch we could check the `reset pagination for this page`. This means that each time you submit the page APEX will set the display rows back to `1~5`.

There are also other ways to reset the pagination automatically. You can reset pagination in the URL: In the `Clear Cache` section you can enter `RP` to clear the page's cache. Please see the [APEX documentation](http://download.oracle.com/docs/cd/E14373_01/appdev.32/e11838/concept.htm#BEIFCDGF) for more information. There is also a page process to reset the pagination. This allows you to also set the scope of the current page, multiple pages, or all pages.

I know the issue has come up to reset the pagination for an individual report rather than the entire page. I currently don't know of any method, however if you do please post!
