---
title: How to Convert Dec to Any Base in SQL
tags:
  - sql
date: 2017-11-06 20:26:15
alias:
---


If you want to convert decimal (base 10) to to hexadecimal (base 16) in SQL you can do so easily with the built in `to_char` functionality:

```sql
select trim(to_char(300, 'XXXXX')) dec2hex
from dual;

DEC2HEX
12C
```

What if you wanted to convert a decimal number to base 20 or any other base? This question recently came up when I was looking to add a [transactional number generator](https://github.com/OraOpenSource/oos-utils/issues/173) for [OOS Utils](https://github.com/OraOpenSource/oos-utils). The goal of the ticket is to convert a sequence number into a human readable number (ex: invoice number). It can easily be solved using PL/SQL (solution below) but I wanted to see if I could do it in SQL as it presents an interesting problem.

## The Formula

For some, this section will go down memory lane to your college / university years where you had to convert dec to hex by hand. It's important to understand the formula to convert dec to hex (or any base),

Using the first example, this is how to convert `300` to `12C` "by hand": _Note `Q` = Quotient, `R` = Remainder_

- `300/16 = Q: 18, R: 12 (hex: C)`
- `18/16 = Q:1, R: 2 (hex: 2)`
- `1/16 = Q: 0, R: 1 (hex: 1)`

The steps above produce the hexadecimal number `12C`. More importantly, each value is dependent on the calculation of the previous line. This isn't easy to do in SQL.

## PL/SQL Function

The following function will convert dec to hex in PL/SQL:

```sql
create or replace function dec2hex (
  p_num in integer)
  return varchar2
as
  l_return varchar2(255);
  l_quotient integer;
  l_remainder integer;
  c_base constant pls_integer := 16;
begin

  l_quotient := p_num;

  while l_quotient > 0 loop
    l_remainder := mod(l_quotient, c_base);
    l_quotient := trunc(l_quotient / c_base);

    -- A=10, B=11, ... F=15
    l_return := substr('0123456789ABCDEF', l_remainder+1, 1) || l_return;
  end loop;

  return l_return;
end dec2hex;
/
```

As you can see PL/SQL uses the quotient from the previous loop to calculate the current loop's remainder.

## SQL Solution

In SQL, finding the value of the previous row is easy using an analytic function such as [`lag`](https://docs.oracle.com/database/122/SQLRF/Analytic-Functions.htm#SQLRF06174). If you've never used analytic functions before, [Connor McDonald](https://twitter.com/connor_mc_d) has a great [series on youtube](https://www.youtube.com/watch?v=xvZ4SmKtazs) about them.

Using the previous row's calculated value for the current row is more difficult and can't be done using analytic functions. This is where the [model clause](https://docs.oracle.com/database/122/SQLRF/SELECT.htm#GUID-CFA006CA-6FF1-4972-821E-6996142A51C6__I2171160) comes in. It's one of the lesser known features in Oracle SQL but very powerful in the right circumstances.

The query below will do dec to hex (or any other "base X") conversion. For demo purposes I have left the `select ... from my_data` portion uncommented to highlight the model clause. To do a full translation uncomment the last few lines.

```sql
var x number;
var base number;

:x := 300;
:base := 16;


with
  -- Find how many loops we'll need to convert dec to basex
  lvls as (
    select level lvl
    from dual
    -- The <= logic will return the number of characters required for conversion
    connect by level <= ceil(log(:base, :x)) + decode(log(:base, :x), ceil(log(:base, :x)), 1,0)
  ),
  -- Alphabet 0..Z
  -- Where 0-9, A=10, B=11 ....
  alphabet as (
    select
      level-1 num,
      case
        when level-1 < 10 then to_char(level-1)
        else chr( ascii('A')+level-1-10)
      end letter
    from dual
    connect by level <= :base
  ),
  -- Returns rows for all the Quotient and Remainder in dec value
  my_data as (
    select
      lvl,
      quotient,
      remainder
    from lvls
    model
      return all rows
      dimension by (lvl)
      measures( 0 remainder, 0 quotient)
      rules (
        -- Order matters here. I.e. R must come after Q so R can "see" Q
        -- cv docs: https://docs.oracle.com/database/122/SQLRF/Model-Functions.htm#SQLRF51210
        quotient[lvl] = trunc(nvl(quotient[cv(lvl)-1], :x) / :base),
        remainder[lvl] = mod(nvl(quotient[cv(lvl)-1], :x), :base)
      )
  )
-- For demo purposes
select md.*, a.letter
from my_data md, alphabet a
where 1=1
  and md.remainder = a.num
order by md.lvl
-- Uncomment below for dec2hex conversion
--select
--  listagg(a.letter, '') within group (order by md.lvl desc) basex
--from my_data md, alphabet a
--where 1=1
--  and md.remainder = a.num
;

LVL   QUOTIENT   REMAINDER LETTER
  1         18          12 C
  2          1           2 2
  3          0           1 1
```



## Final Thoughts

The model clause is extremely powerful when used to solve problems it was intended for. The toughest thing to do is learn and understand how it works with all it's options/function. If it's any help, I'm still learning more about the model clause and by no means an expert.

If you do some interesting things with a model clause please blog about it and send me a message as I can list future posts in this article.
