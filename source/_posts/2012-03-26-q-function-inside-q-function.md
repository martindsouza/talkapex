---
title: q Function Inside a q Function
tags:
  - PL/SQL
date: 2012-03-26 07:00:00
alias:
---

Two years ago I wrote about how to escape single quotes in string using the _q_ function: [http://www.talkapex.com/2009/03/q-function-escape-single-quotes.html](http://www.talkapex.com/2009/03/q-function-escape-single-quotes.html) If you've never seen this before or don't know what I'm talking about please read the article first before continuing.

I recently had an issue where I had to use a _q_ function inside another _q_ function. Here's what I tried: 
<pre class="brush: sql;">DECLARE
  l_code varchar2(4000);

BEGIN
  l_code := q'!BEGIN dbms_output.put_line(q'!It's cold in Calgary!'); END; !';

  dbms_output.put_line(l_code);

  execute immediate l_code;

END;
/

ERROR:
ORA-01756: quoted string not properly terminated
</pre>You'll notice that the above code doesn't run. The reason it doesn't work is that I'm using the same character (!) as the quote delimiter. To fix this, each "Q" block must have it's own unique quote delimiter. Below is a copy of the same code but using two different quote delimiters (# and !).   
<pre class="brush: sql;">DECLARE
  l_code varchar2(4000);

BEGIN
  l_code := q'#BEGIN dbms_output.put_line(q'!It's cold in Calgary!'); END; #';

  dbms_output.put_line(l_code);

  execute immediate l_code;

END;
/

BEGIN dbms_output.put_line(q'!It's cold in Calgary!'); END;
It's cold in Calgary

PL/SQL procedure successfully completed.
</pre>For more information on the q function (it's real name is "Q-quote mechanism") read the Oracle documentation: [http://docs.oracle.com/cd/B19306_01/appdev.102/b14251/adfns_sqltypes.htm#BABECADE](http://docs.oracle.com/cd/B19306_01/appdev.102/b14251/adfns_sqltypes.htm#BABECADE) and scroll down to the "Quoting Character Literals" section.