---
title: 'APEX: Saving item values for each user'
tags:
  - apex
date: 2009-07-02 09:00:00
alias:
---

Someone asked me today if APEX could remember input values for specific page items. For example if you have a page with report parameters could APEX remember the report parameters that the user last used the next time they logged in?

<span style="font-style:italic; color:red;">Note: Please read comments below as APEX does support this out of the box on an individual item basis. This solution is to make the option configurable for large applications.</span>

APEX doesn't support this out of the box, however it does have some great features which can enable you to do this. <span style="font-style=italic;">You can use cookies for this but I wanted to make the solution work no matter where the user was accessing the application from.</span>

To make things a bit more difficult, I don't want to remember all item values on a page so I must be able to control which items are "remembered" and which items aren't. I can do this by using a naming convention in my items, however I don't want to rename all my page items (I already have a lot of them). Instead I decided to create a table which will list all the items a user can remember.

You can try the demo [here](http://apex.oracle.com/pls/otn/f?p=20195:1800) (follow the instructions on the page).

<pre class="brush: sql">
CREATE TABLE tapex_remember_page_item(
application_id NUMBER NOT NULL,
page_id NUMBER NOT NULL,
item_name VARCHAR2(255) NOT NULL);

-- You don't need to add a UK, however it may be a good idea.
ALTER TABLE tapex_remember_page_item ADD(
  CONSTRAINT tapex_remember_page_item_uk1
  UNIQUE (application_id, page_id, item_name));

-- Since I name all my APEX items in uppercase, just do this as an extra precaution
CREATE OR REPLACE TRIGGER trg_tapex_remember_pg_itm_buir
  BEFORE UPDATE OR INSERT
  ON tapex_remember_page_item
  FOR EACH ROW
BEGIN
  :NEW.item_name := UPPER (:NEW.item_name);
END;
/

INSERT INTO tapex_remember_page_item
            (application_id, page_id, item_name)
     VALUES (20195, 1800, 'P1800_DEPTNO');

INSERT INTO tapex_remember_page_item
            (application_id, page_id, item_name)
     VALUES (20195, 1800, 'P1800_MIN_SAL');            
</pre>

For this example we'll store the values as APEX Preferences, however you could easily create your own preferences table to manage your data. I think they're several advantages to managing the preferences in your own table, however if you have a small application with a limited number of users then I'd recommend using the APEX_UTIL preference options            

Create 2 Application Processes:

AP_GET_PAGE_ITEM_PREFS
On Load: Before Header (page template header)

<pre class="brush: sql">
DECLARE
BEGIN
  FOR x IN (SELECT item_name
              FROM tapex_remember_page_item
             WHERE :app_page_id = page_id
               AND :app_id = application_id) LOOP
    apex_util.set_session_state (p_name      => x.item_name,
                                 p_value     => apex_util.get_preference (p_preference     => x.item_name,
                                                                          p_user           => :app_user
                                                                         )
                                );
  END LOOP;
END;
</pre>

AP_SET_PAGE_ITEM_PREFS
On Submit: After Page Submission - After Computations and Validations

<pre class="brush: sql">
DECLARE
BEGIN
  FOR x IN (SELECT item_name
              FROM tapex_remember_page_item
             WHERE :app_page_id = page_id
               AND :app_id = application_id) LOOP
    apex_util.set_preference (p_preference => x.item_name, p_value => v (x.item_name), p_user => :app_user);
  END LOOP;
END;
</pre>

<span style="font-style=italic;">For those of you that are curious APEX Preferences are stored in : apex_030200.wwv_flow_preferences$ where apex_030200 is the schema name for APEX (could also be called flows_xxxxxx)</span>
