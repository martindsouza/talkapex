---
title: Validating Dates and Numbers in Oracle 12.2
tags:
  - sql
  - plsql
  - oracle-12c
date: 2019-03-26 15:16:41
alias:
---


Prior to 12.2 validating dates and numbers was a bit of a pain as you had to write your own custom PL/SQL function. [OOS-Utils](http://github.com/oraopenSource/oos-utils/) has a quick solution to this issue and I recommend using [`oos_util_validation.is_number`](https://github.com/OraOpenSource/oos-utils/blob/master/docs/oos_util_validation.md#is_number) and [`oos_util_validation.is_date`](https://github.com/OraOpenSource/oos-utils/blob/master/docs/oos_util_validation.md#is_date).

If you're using Oracle 12.2 or above you can (and should) use [`validation_conversion`](https://docs.oracle.com/en/database/oracle/oracle-database/18/sqlrf/VALIDATE_CONVERSION.html#GUID-DC485EEB-CB6D-42EF-97AA-4487884CB2CD) instead. Here are some examples of how to use it along with results:

```sql
select
  validate_conversion('123.34' as number, '999.00') valid_number,
  validate_conversion('abc' as number) invalid_number,
  validate_conversion('01-Jan-19' as date, 'DD-MON-YYYY') valid_date,
  validate_conversion('01-BAD-19' as date, 'DD-MON-YYYY') invalid_date
from dual
;

VALID_NUMBER INVALID_NUMBER VALID_DATE INVALID_DATE
------------ -------------- ---------- ------------
           1              0          1            0
```

`validate_conversion` will return `1` for valid conversions and `0` for invalid conversions. 

_Note: in the next release of OOS Utils, the validation functions will use_ `validate_conversion` _if your database is 12.2 or older internally._