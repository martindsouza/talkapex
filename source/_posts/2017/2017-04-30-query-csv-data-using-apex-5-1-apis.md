---
title: Query CSV data using APEX 5.1 APIs
tags:
  - plsql
  - sql
  - apex-5.1
  - apex
date: 2017-04-30 05:42:48
alias:
---


When I first started using Oracle many years ago one of the most frustrating things was the amount of code and complexity required to parse/query CSV data (or any delimited data). As time passed I was able to leverage new features and techniques to help but they all had their issues. APEX 5.1 introduced a new API that may help simplify a lot of the headaches. The following example highlights this.

Suppose I had the following CSV data:

```
First Name, Last Name, Dept
Martin, DSouza, IT
John, Doe, Sales
Sally, Smith, Sales
```

The first thing to do is break each of the rows of text into rows of a query. This can be done using the new APEX 5.1 [`apex_string.split`](https://docs.oracle.com/database/apex-5.1/AEAPI/SPLIT-Function-Signature-1.htm#AEAPI-GUID-3BE7FF37-E54F-4503-91B8-94F374E243E6) API

```sql
select *
from table(apex_string.split(:data,chr(10)))
;

-- Results in
COLUMN_VALUE                                
------------------------------
First Name, Last Name, Dept
Martin, DSouza, IT
john, doe, Sales
Sally, Smith, Sales
```

Where `:data` is the CSV data above. _Note I'm using a Mac and the EOL character is a `LF` (`chr(10)`). Windows users use `CR + LF` (`chr(13) || chr(10)`). More info on this [here](https://en.wikipedia.org/wiki/Newline)_

Now that each line of data is on it's own row we need to create columns. This can be done using regular expressions:

```sql
select
  rtrim(regexp_substr(column_value, '([^,])*(,)?', 1, 1), ',') fname,
  rtrim(regexp_substr(column_value, '([^,])*(,)?', 1, 2), ',') lname,
  rtrim(regexp_substr(column_value, '([^,])*(,)?', 1, 3), ',') dept
from table(apex_string.split(:data,chr(10)))
;

-- Results in
FNAME        LNAME       DEPT  
First Name   Last Name	 Dept               
Martin	     DSouza      IT                       
john         doe         Sales
Sally        Smith       Sales
```

To remove the header row change the query to:

```sql
select fname, lname, dept
from 
  (
    select 
      rownum rn,
      regexp_substr(column_value, '[^,]+', 1, 1) fname,
      regexp_substr(column_value, '[^,]+', 1, 2) lname,
      regexp_substr(column_value, '[^,]+', 1, 3) dept
    from table(apex_string.split(:data,chr(10)))
  )
where rn > 1
```


