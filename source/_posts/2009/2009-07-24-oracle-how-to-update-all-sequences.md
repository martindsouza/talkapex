---
title: 'Oracle: How to update all sequences'
tags:
  - plsql
  - oracle
date: 2009-07-24 09:00:00
alias:
---

If you ever do data refreshes from production to development or test environments you may run into an issue where your sequences are not up to date. It seems that Oracle exports the sequences first, then the data. If your sequence numbers change during the entire export process you may get errors when using them in your refreshed schema.

To fix this problem you can try to find where your sequences are used and get the MAX(value) to find the next value. Alternatively you can just add a large random number, say 1,000,000, to all your sequences. For most users this will fix the problem and is very easy to do. Here's how:

<pre class="brush: sql">
-- Update all sequences
DECLARE
  v_increase_by                     NUMBER;
  v_bkp_increment_by            NUMBER;
  v_str                         VARCHAR2 (1000);
  v_count                       NUMBER;
BEGIN
  v_increase_by                  := 1000000;

  FOR rec IN (SELECT *
              FROM   user_sequences) LOOP
    -- Backup current incrementation number
    v_bkp_increment_by         := rec.increment_by;
    -- Alter the sequence to increase by a defined amount
    v_str                      := 'alter sequence ' || rec.sequence_name || ' increment by ' || v_increase_by;

    EXECUTE IMMEDIATE v_str;

    -- Increase by that amount
    v_str                      := 'select ' || rec.sequence_name || '.nextval from dual';

    EXECUTE IMMEDIATE v_str
    INTO              v_count;

    -- Reset the increment factor
    v_str                      := 'alter sequence ' || rec.sequence_name || ' increment by ' || v_bkp_increment_by;

    EXECUTE IMMEDIATE v_str;
  END LOOP;
END;
/
</pre>
