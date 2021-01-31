---
title: The Biggest Hidden Bug in Your APEX Application
tags:
  - apex
  - sql
  - oracle
date: 2021-01-31 13:03:23
alias:
---


Let's start with a little quiz. Without checking, what do you think happens/returns running the following query:

```sql
-- Note the two digit year
select to_date('24-Jan-21', 'DD-MON-YYYY')
from dual;
```

I recently [asked this same question](https://twitter.com/martindsouza/status/1353550672919023617) on Twitter and the results were quite interesting given that this *should* be a very straight forward answer. Over 50% of the people got it wrong:

{% asset_img twitter-results.png %}


The correct answer is that the query returns: `24-Jan-0021`. If you thought an error would be raised you're in good company. I reached out to several [Oracle ACE](https://developer.oracle.com/ace/)s (i.e. world leading Oracle experts) and they all got it wrong as well (myself included).

By using a date format of `DD-MON-YYYY` if a user does not explicitly enter a four digit year, Oracle will left pad the number with `0`s. I.e. `21` becomes `0021`. This makes sense that one would expect `5-Jan-2021` to really be `05-Jan-2021`. 

What does this have to do with your APEX application? In all my applications I tend to set the default date format to `DD-MON-YYYY` as it's very explicit. *Note: to set the default date format in APEX go to `Shared Components > Globalization > Application Date Format`.* Most of the Date items that I use allow users to either enter the date manually or select from the date picker (via button click). Since entering a two digit year is a valid date neither APEX or Oracle raises an error so it's extremely difficult to catch.

To get around this issue you can change the Application Date Format to: `DD-MON-RRRR`. From the [Oracle documentation](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Format-Models.html#GUID-6C75461E-2E18-4C35-9EB4-038A7E1C9C1F):

*The RR datetime format element is similar to the YY datetime format element, but it provides additional flexibility for storing date values in other centuries. The RR datetime format element lets you store 20th century dates in the 21st century by specifying only the last two digits of the year.* 

Using our initial example

```sql
select to_date('24-Jan-21', 'DD-MON-YYYY')
from dual;

-- Returns
24-jan-2021
```

The nice thing about using the `RRRR` format is you can still display years as four digits but users can enter them in as two digits with expected results. If users are entering past dates (ex: cataloging old library books) I suggest you read the [documentation](https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/Format-Models.html#GUID-6C75461E-2E18-4C35-9EB4-038A7E1C9C1F) to see if the `RRRR` format is the right setting for your application.



