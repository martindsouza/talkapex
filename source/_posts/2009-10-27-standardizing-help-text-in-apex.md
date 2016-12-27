---
title: Standardizing Help Text in APEX
tags:
  - APEX
  - ORACLE APEX
date: 2009-10-27 09:00:00
alias:
---

[![](http://2.bp.blogspot.com/_33EF80fk9sM/SuaEhEG5sJI/AAAAAAAADtY/62XBUIfwlE0/s200/help_key.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/SuaEhEG5sJI/AAAAAAAADtY/62XBUIfwlE0/s1600-h/help_key.jpg)
I had a requirement where the same item name was used in various areas of the application. To maintain consistancy, and to minimize development time, I decided to create a "help framework" to standardize on the help text for these items.

<span style="font-style:italic">Note: I know APEX comes with a great tool to maintain consistancy for your page items called User Interface Defaults, however it would not work in this case.</span>

The goal was for developers not to have to enter any help text for commonly named page items. Instead, when applicable, the help text would automatically be retreived from a common area so we'd only need to maintain it in one place. The design also had to handle custom help text (i.e. if we needed to override the default help text).

To do this I used Tool Tip Help (<span style="font-style:italic;">see: [http://apex-smb.blogspot.com/2009/09/tooltip-help-in-apex-alternative-to.html](http://apex-smb.blogspot.com/2009/09/tooltip-help-in-apex-alternative-to.html)</span>) and made the following modifications:

<span style="font-weight:bold;">- Create a Default Help Page</span>
Page Name: Default Help Page

Create a region called "Default Help Items" and add all the items you want default help text for. For example, I created P10 and added the following items: P10_EMPNO, P10_ENAME. In each of these items I added the default help text for similarly named items.

<span style="font-weight:bold;">- Modify the Application Process AP_GET_HELP_TEXT </span>
<pre class="brush: sql">
BEGIN
  FOR x IN (SELECT    '<span class="itemToolTip" foritem="'
                   || item_name
                   || '">'
                   || item_help_text
                   || '</span>' help_html
              FROM (SELECT a.item_name,
                           NVL (a.item_help_text, dflt_help_text.item_help_text)
                                                                           item_help_text
                      FROM apex_application_page_items a,
                           (SELECT 'P' || :app_page_id || '_' || LTRIM (item_name, 'P10_')
                                                                                item_name,
                                   item_help_text
                              FROM apex_application_page_items
                             WHERE application_id = :app_id
                               AND page_id = 10 -- Enter Default Help Page number here (and modify select statement above)
                               AND item_help_text IS NOT NULL) dflt_help_text
                     WHERE a.application_id = :app_id
                       AND a.page_id = :app_page_id
                       AND a.item_name = dflt_help_text.item_name(+)
                       AND NVL (a.item_help_text, 'null') != '@NO_HELP@')
             WHERE item_help_text IS NOT NULL)
  LOOP
    HTP.p (x.help_html);
  END LOOP;
END;
</pre>

Now when I run the application, any item that is called PXX_EMPNO or PXX_ENAME will try to use the help text from P10 for the corresponding items. Developers can easily override the default help text by filling in specific help text for an item or by entering in "@NO_HELP@"