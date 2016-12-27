---
title: Conditional Validations in APEX
tags:
  - APEX
  - ORACLE APEX
date: 2010-06-17 09:00:00
alias:
---

[![](http://2.bp.blogspot.com/_33EF80fk9sM/TBbbcfsk2gI/AAAAAAAADxA/NuLp-l-YU1I/s400/blue-check-mark1.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/TBbbcfsk2gI/AAAAAAAADxA/NuLp-l-YU1I/s1600/blue-check-mark1.jpg)
There may be some instances when you have validations on your APEX page that you only want to run if all the other validations have passed. A good example of this is if you have 2 fields, FROM_DATE and TO_DATE and have the following validations:
- V1: Ensure FROM_DATE is not null and is a valid date
- V2: Ensure TO_DATE is not null and is a valid date
- V3: Ensure FROM_DATE < TO_DATE

In this example you only want the last validation to run if the first 2 pass (i.e. both FROM_DATE and TO_DATE are valid dates). Currently there's no declarative way of doing this. i.e. validations don't support declarative dependencies.

A simple trick to get around this issue is to put a condition in some validations to only run if all the other validations pass. This may not work in all situations, however it should help you in some.

In the example about, modify the 3rd validation (V3) and set the condition to the following:
Condition Type: PL/SQL Expression
Expression 1: apex_application.g_inline_validation_error_cnt = 0

Now V3 will only run if all previous validations pass.