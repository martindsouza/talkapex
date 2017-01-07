---
title: Manually Creating a Tabular Form
tags:
  - apex
date: 2008-07-31 21:11:00
alias:
---

When I first started developing in APEX (back when it was called HTMLDB) I had a requirement for a tabular form. I tried to use the standard tabular forms but it was very limited and I couldn't customize it to meet my requirements. I had poked around on the APEX forum, but wasn't able to find a reasonable solution.

After several iterations, I have come up with a process that works extremely well. It is slightly labor intensive but it has met all the requirements each time. Please note that Patrick Wolf, http://www.inside-oracle-apex.com/, has developed an open source framework to handle customized tabular forms. Though it is very good it does not allow for complete access. Marcie Young did a presentation at ODTUG 2008 which outlined a very similar method, this example is taking it a step further. A working example is available here: [http://apex.oracle.com/pls/otn/f?p=20195:200](http://apex.oracle.com/pls/otn/f?p=20195:200)

The process below will not only build a customized tabular form, but also handle the errors etc.

<span style="font-weight: bold;">Overview</span>
Here's an overview of the overall methodology
- Create 2 collections. One will handle data: <span style="font-style: italic;">DATA_COLLECTION</span> and one will handle the errors: <span style="font-style: italic;">ERROR_COLLECTION</span>.
- The <span style="font-style: italic;">DATA_COLLECTION</span> will be loaded on the first viewing of the page and will only be refreshed from the database when the changes have been sent to the database.
- The <span style="font-style: italic;">ERROR_COLLECTION</span> will contain errors specific for the corresponding entry in the <span style="font-style: italic;">DATA_COLLECTION</span>. We could keep this in the same collection, but I like to keep them separate. It makes things easier if we need to add or remove columns.
- In the <span style="font-style: italic;">ERROR_COLLECTION</span> I keep column 50 reserved for the row error. An example of when you'd need a row error is when you have a start and end date and the end date is before the start date.
- This application will allow users to modify the emp.ename and emp.salary fields
- In the data collection I keep column 1 reserved for the sequence id (seq_id). Note though I won't use it in this example, it has come in handy (especially when hiding/delete rows in the front end then submitting the page)

<span style="font-weight: bold;">Getting Started</span>

Step 1: Create a PL/SQL Process <span style="font-style: italic;">On Load Before Header</span> called <span style="font-style:italic;">Create Collection</span>
<pre class="brush: sql;">
DECLARE
BEGIN
   IF NVL (:p200_reload_flag, 'N') = 'N'
   THEN
      -- IF error collection exists, delete
      IF apex_collection.collection_exists
                                     (p_collection_name      => 'ERROR_COLLECTION')
      THEN
         apex_collection.delete_collection
                                     (p_collection_name      => 'ERROR_COLLECTION');
      END IF;

      -- Create New Collection
      apex_collection.create_or_truncate_collection ('DATA_COLLECTION');

      FOR rec IN (SELECT   e.empno, e.ename, e.sal
                      FROM emp e
                  ORDER BY e.ename)
      LOOP
         apex_collection.add_member (p_collection_name      => 'DATA_COLLECTION',
                                     p_generate_md5         => 'NO',
                                     p_c002                 => rec.empno,
                                     p_c003                 => rec.ename,
                                     p_c004                 => TO_CHAR
                                                                      (rec.sal),
                                     -- Remember the collection is only text
                                     p_c049                 => 'Y',
                                     -- Modifiable Flag
                                     p_c050                 => 'U'
                                    -- SQL Action (Insert, Update, Delete)
                                    );
      END LOOP;
   --
   END IF;

-- Create Extra rows (if we wanted to add a new employee
   FOR i IN 1 .. NVL (:p200_num_extra_rows, 0)
   LOOP
      apex_collection.add_member (p_collection_name      => 'DATA_COLLECTION',
                                  p_generate_md5         => 'NO',
                                  p_c002                 => -1,
                                  -- use negative numbers for new employees
                                  p_c049                 => 'Y',
                                  p_c050                 => 'I'
                                 );
   END LOOP;

   -- Insert seq_id
   FOR rec IN (SELECT ac.seq_id
                 FROM apex_collections ac
                WHERE ac.collection_name = 'DATA_COLLECTION')
   LOOP
      apex_collection.update_member_attribute
                                   (p_collection_name      => 'DATA_COLLECTION',
                                    p_seq                  => rec.seq_id,
                                    p_attr_number          => 1,
                                    p_attr_value           => rec.seq_id
                                   );
   END LOOP;
END;
</pre>
Step 2: Create a <span style="font-style:italic;">Report / SQL Report</span> region for our custom tabular form called <span style="font-style:italic;">Employee Data</span>
<pre class="brush: sql;">
SELECT
   -- Notice how I'm keeping the idx value the same as the column value in the collection. This helps to keep things organized
   -- I also apply an id to each entry
   -- I append the error value to the empname and sal
   -- The Seq_id. Usefull when hiding rows (for delete) and then submitting from
   apex_item.hidden(1,x.seq_id, null, x.seq_id || '_seq_id') ||
   -- The Primary Key of the column
   apex_item.hidden(2, x.empno, null, x.seq_id || '_empno_id') || x.empno empno,
   -- Employee Name
   apex_item.text(3,x.empname,null, null, null, x.seq_id || '_empname_id') || err.empname employee_name,
   -- Employee Salary
   apex_item.text(4,x.sal, null, null, null, x.seq_id || '_sal_id') || err.sal ||
   -- Store the sql action type as well.
   apex_item.hidden(50,x.sql_action_typ, null, x.seq_id || '_sql_action_typ_id') sal,
   -- Last but not least the row error
   err.row_error
FROM   (SELECT  ac.c001 seq_id,
                ac.c002 empno,
                ac.c003 empname,
                ac.c004 sal,
                ac.c049 modifiable_flag,
                ac.c050 sql_action_typ
       FROM     apex_collections ac
       WHERE    ac.collection_name = 'DATA_COLLECTION'
       ORDER BY ac.seq_id) x,

      -- Error Collection
      (SELECT   ac.seq_id seq_id,
                ac.c002 empno,
                ac.c003 empname,
                ac.c004 sal,
                ac.c050 row_error -- Useful when individual data is correct, however the row of data is not. Ex: start/end dates
       FROM     apex_collections ac
       WHERE    ac.collection_name = 'ERROR_COLLECTION'
       ORDER BY ac.seq_id) err
WHERE x.seq_id = err.seq_id(+)         
</pre>
Step 3: Add Region items and buttons

All items/buttons should be added to the report region
Create Buttons:
<span style="font-style:italic;">ADD</span>, submit page. Branch to <span style="font-style:italic;">&APP_PAGE_ID.</span>
<span style="font-style:italic;">SUBMIT</span>, submit page. Branch to <span style="font-style:italic;">201</span>
Note: Page <span style="font-style:italic;">201</span> is a simple sql report for <pre class="brush: sql;">
SELECT *
FROM EMP
</pre>
Create <span style="font-style:italic;">Hidden and Protected</span> Items:
<span style="font-style:italic;">P200_DISPLAY_ROW_ERROR_FLAG</span>
- Source value or expression: N
- Comment: Used to determine if the error column should be displayed
<span style="font-style:italic;">P200_NUM_EXTRA_ROWS</span>
- Source value or expression: 1
- Comment: Number of extra rows to add to the tabular form
<span style="font-style:italic;">P200_RELOAD_FLAG</span>
- Comment: If Y then we won't refresh the collection with database values

Computations (After Submit)
<span style="font-style:italic;">P200_NUM_EXTRA_ROWS</span>
Static: 1
Condition: Request = ADD

<span style="font-style:italic;">P200_NUM_EXTRA_ROWS</span>
Static: 0
Condition: Request != ADD

<span style="font-style:italic;">P200_RELOAD_FLAG</span>
Static: Y
Condition: None

<span style="font-style:italic;">P200_DISPLAY_ROW_ERROR_FLAG</span>
Static: N
Condition: None

Step 4: Store Collection
After submit, this will store the data from the form into the collection. No data validation is performed at this point

Process: <span style="font-style:italic;">Store Collection</span>
Type: PL/SQL Anonymous Block
Processing Point: On Submit: Before Computation and Validations
Source:
<pre class="brush: sql;">
DECLARE
BEGIN
 FOR i IN 1 .. apex_application.g_f01.COUNT LOOP
   apex_collection.update_member (p_collection_name            => 'DATA_COLLECTION',
-- I know some of you are still wondering why were are still wondering why I stored the seq_id as a collection attribute. This is why. If you had hidden the row (i.e. let the user "delete" it) then it would not show up on this and your collection synchronization wouldn't be correct.
          p_seq                        => apex_application.g_f01 (i), -- Sequence ID
                                  p_c001                       => apex_application.g_f01 (i), -- Sequence ID
                                  p_c002                       => apex_application.g_f02 (i), -- Empno
                                  p_c003                       => apex_application.g_f03 (i), -- Empname
                                  p_c004                       => apex_application.g_f04 (i), -- Sal
                                  p_c049                       => 'Y',   -- Modifiable Flag
                                  p_c050                       => UPPER(apex_application.g_f50 (i))
                                 );
 END LOOP;
END;
</pre>
Step 5: Validation

Type: Page Level Validation
Type: PL/SQL - Function returning Error Text
Name: Validate Collection
Condition: When Button Pressed - SUBMIT
Validation Expression:
<pre class="brush: sql;">
DECLARE
   v_err_msg   VARCHAR2 (255);
BEGIN
   -- IF error collection exists, truncate. Else Create
   IF apex_collection.collection_exists
                                     (p_collection_name      => 'ERROR_COLLECTION')
   THEN
      apex_collection.truncate_collection
                                     (p_collection_name      => 'ERROR_COLLECTION');
   ELSE
      -- Create Error Collection
      apex_collection.create_or_truncate_collection ('ERROR_COLLECTION');
   END IF;

   -- Basic Check. Make sure the emp name is more than 5 chars long
   FOR x IN (SELECT ac.c003 empname, ac.c004 sal, ac.seq_id
               FROM apex_collections ac
              WHERE ac.collection_name = 'DATA_COLLECTION')
   LOOP
      -- Always add a blank error
      apex_collection.add_member (p_collection_name => 'ERROR_COLLECTION');

      IF LENGTH (x.empname) < 5
      THEN
         apex_collection.update_member
            (p_collection_name      => 'ERROR_COLLECTION',
             p_seq                  => x.seq_id,
             p_c003                 => '
<span style="color:red">Name must be 5 Chars</span>'
            );
         v_err_msg := 'Error Occured';
      END IF;

      -- Add a "row level" check for demo purposes
      IF LENGTH (x.empname) = LENGTH (x.sal)
      THEN
         apex_collection.update_member
             (p_collection_name      => 'ERROR_COLLECTION',
              p_seq                  => x.seq_id,
              p_c050                 => '
<span style="color:red">Row Level Error</span>'
             );
         apex_util.set_session_state (p_name       => 'P200_DISPLAY_ROW_ERROR_FLAG',
                                      p_value      => 'Y'
                                     );
         v_err_msg := 'Error Occured';
      END IF;
   END LOOP;

   RETURN v_err_msg;
END;
</pre>
Step 6: Finishing it off

As you notice there's a column called: <span style="font-style:italic;">row_error</span>. Set the condition where item: <span style="font-style:italic;">P200_DISPLAY_ROW_ERROR_FLAG = Y</span>.
