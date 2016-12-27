---
title: Enhancing APEX Security
tags:
  - APEX
date: 2009-05-11 07:27:00
alias:
---

<span style="font-style:italic;color:red;">Note: Full explanation available [here](http://apex-smb.blogspot.com/2009/05/enhancing-apex-security-explanation.html). Please read before continuing with this post.</span>

The best way to secure data in your APEX application, or any application, is to secure your data in the database. You can do this using Oracle's Virtual Private Database (VPD), also known as Fine Grained Access Control (FGAC). APEX also has Session State Protection (SSP) which helps prevent front-end modification of data by preventing URL tampering.

I'm a big advocate of using both VPD and SSP. In some situations VPD may not be implemented for various reasons so developers must rely on SSP. SSP is great and allows developers to quickly and easily help prevent malicious users from trying to access data that they aren't supposed to. It's important to note that SSP only prevents URL tampering. If users wanted to alter data on a form they could easily do that with Firebug (I won't go into that in this post). SSP also prevents users from setting values using AJAX. This can be circumvented using the "x01" .. "x10" parameters (please see Carl's [post](http://carlback.blogspot.com/2008/03/new-stuff-2-x01-and-friends.html) for more info) but still leaves the door open for users to alter items in session state that they shouldn't.

How can you use SSP, make AJAX calls, and ensure that users aren't altering data on a form? This question has been bugging me for a while and I think I've come up with a solution that should work with minimal changes to existing code. For this example I'm going to use a select list containing a list of IDs (this is where the issue has come up the most for me). I'll use the emp table, with empno as the id, for this example.

Some notation before we begin: "Secure" items (i.e. ones that have a hashed value appended to them) will be called PX_ITEM_NAME_SEC while it's corresponding unsecured item is called PX_ITEM_NAME. The code only looks at "secure" items that have a matching "unsecured" item. "Unsecure" items are really items that are Hidden and Protected so they have a checksum associated to them.

The demo can be found [here](http://apex.oracle.com/pls/otn/f?p=20195:1500)

<span style="font-weight:bold;">Step 1- Compile Package</span>

<span style="font-style:italic">Since I don't have access to DMBS_CRYPTO on apex.oracle.com I've used a dummy encryption method.</span> You'll need to grant execute on DBMS_CRYPTO from SYS:

<pre class="brush: sql">
GRANT EXECUTE ON DBMS_CRYPTO TO giffy; -- Where "giffy" is your schema name
</pre>

Compile the following code in your schema 

<span style="font-style:italic;">pkg_apex_sec.pks</span>
<pre class="brush: sql">
CREATE OR REPLACE PACKAGE pkg_apex_sec
AS
  /**
   * Returns secure value
   * @param p_value
   * @return
   */
  FUNCTION f_get_sec_val (
    p_value IN VARCHAR2
  )
    RETURN VARCHAR2;

  /**
   * Checks if secured value is valid
   * @param p_hashed_val (case sensitive
   * @return 'Y' or 'N'
   */
  FUNCTION is_valid_hashed_val (
    p_hash IN VARCHAR2
  )
    RETURN VARCHAR2;

  /**
   * unsecure value given the hash
   * @param p_hash
   * @return unsecure number
   */
  FUNCTION f_get_val (
    p_hash IN VARCHAR2
  )
    RETURN VARCHAR2;

   /**
  * Sets unsec values in the page given the secure values
  * @param p_page_id Page ID to set. Default current page
  */
  PROCEDURE sp_set_page_unsec_values (
    p_page_id IN apex_application_pages.page_id%TYPE DEFAULT v ('APP_PAGE_ID')
  );

  /**
   * Set all the secure values given the unsecure values
   * @param p_page_id Page ID. Default current page
   */
  PROCEDURE sp_set_page_sec_values (
    p_page_id IN apex_application_pages.page_id%TYPE DEFAULT v ('APP_PAGE_ID')
  );
END pkg_apex_sec;
</pre>

<span style="font-style:italic;">pkg_apex_sec.pkb</span>
<pre class="brush: sql">
CREATE OR REPLACE PACKAGE BODY pkg_apex_sec
AS
  -- Constants
  gc_delim CONSTANT VARCHAR2 (1) := '@';

/**
 * Returns hashed value
 * May require sys to grant access to dbms_crypto
 *  - GRANT EXECUTE ON DBMS_CRYPTO TO <schema>;
 * @param p_source
 * @param p_key
 * @return hashed value
 */
  FUNCTION f_get_md5 (
    p_source IN VARCHAR2,
    p_key IN VARCHAR2
  )
    RETURN VARCHAR2
  IS
    v_key VARCHAR2 (4000) := p_key;
  BEGIN
    -- Normally your key should be different from the source. (this is up to you to maintain)
    -- For simplicity this function will see if they are the same. If so we'll append the app_id
    IF p_source = p_key THEN
      v_key := p_key || v ('APP_ID');
    END IF;

    -- Can't use DBMS_CRYPTO in apex.oracle.com. Using generic coding
    -- RETURN DBMS_CRYPTO.mac (src => UTL_RAW.cast_to_raw (p_source), typ => 2, KEY => UTL_RAW.cast_to_raw (v_key));
    RETURN p_source || p_key || 123;                                                                     -- DELETE THIS.
  END f_get_md5;

/**
 * Returns secure value
 * @param p_value
 * @return
 */
  FUNCTION f_get_sec_val (
    p_value IN VARCHAR2
  )
    RETURN VARCHAR2
  AS
  BEGIN
    -- For the Key value I'm arbitrarily appending the app id. You should change this to something that is secure to your code.
    -- You can add :app_session as well, provided you're not using session 0 or external links (ie. from emails etc)
    RETURN p_value || gc_delim || f_get_md5 (p_source => p_value, p_key => p_value || v ('APP_ID'));
  END f_get_sec_val;

  /**
   * Checks if secured value is valid
   * @param p_hashed_val (case sensitive
   * @return 'Y' or 'N'
   */
  FUNCTION is_valid_hashed_val (
    p_hash IN VARCHAR2
  )
    RETURN VARCHAR2
  AS
    v_value VARCHAR2 (4000);
  BEGIN
    v_value := REPLACE (REGEXP_SUBSTR (p_hash, '^[[:print:]]+' || gc_delim), gc_delim);

    IF p_hash = f_get_sec_val (p_value => v_value) THEN
      RETURN 'Y';
    ELSE
      RETURN 'N';
    END IF;
  EXCEPTION
    WHEN OTHERS THEN
      RETURN 'N';
  END is_valid_hashed_val;

  /**
   * unsecure value given the hash
   * @param p_hash
   * @return unsecure number
   */
  FUNCTION f_get_val (
    p_hash IN VARCHAR2
  )
    RETURN VARCHAR2
  AS
  BEGIN
    IF is_valid_hashed_val (p_hash => p_hash) = 'N' THEN
      RETURN NULL;
    END IF;

    RETURN (REPLACE (REGEXP_SUBSTR (p_hash, '^[[:print:]]+' || gc_delim), gc_delim));
  EXCEPTION
    WHEN OTHERS THEN
      RETURN NULL;
  END f_get_val;

  /**
   * Sets unsec values in the page given the secure values
   * @param p_page_id Page ID to set. Default current page
   */
  PROCEDURE sp_set_page_unsec_values (
    p_page_id IN apex_application_pages.page_id%TYPE DEFAULT v ('APP_PAGE_ID')
  )
  AS
    v_app_id apex_applications.application_id%TYPE := v ('APP_ID');
  BEGIN
    BEGIN
      -- Set all the unsecure values from the secure values
      FOR x IN (SELECT a1.item_name item_name_sec,
                       a2.item_name
                  FROM apex_application_page_items a1,
                       apex_application_page_items a2
                 WHERE a1.application_id = v_app_id
                   AND a1.page_id = p_page_id
                   AND a1.item_name LIKE '%_SEC'
                   -- Find corresponding item name
                   AND a2.application_id = a1.application_id
                   AND a2.page_id = a1.page_id
                   AND RTRIM (a1.item_name, '_SEC') = a2.item_name) LOOP
        apex_util.set_session_state (x.item_name, pkg_apex_sec.f_get_val (v (x.item_name_sec)));
      END LOOP;
    END;
  END sp_set_page_unsec_values;

  /**
   * Set all the secure values given the unsecure values
   * @param p_page_id Page ID. Default current page
   */
  PROCEDURE sp_set_page_sec_values (
    p_page_id IN apex_application_pages.page_id%TYPE DEFAULT v ('APP_PAGE_ID')
  )
  AS
    v_app_id apex_applications.application_id%TYPE := v ('APP_ID');
  BEGIN
    -- Set all the secure values from the secure values
    FOR x IN (SELECT a1.item_name item_name_sec,
                     a2.item_name
                FROM apex_application_page_items a1,
                     apex_application_page_items a2
               WHERE a1.application_id = v_app_id
                 AND a1.page_id = p_page_id
                 AND a1.item_name LIKE '%_SEC'
                 -- Find corresponding item name
                 AND a2.application_id = a1.application_id
                 AND a2.page_id = a1.page_id
                 AND RTRIM (a1.item_name, '_SEC') = a2.item_name) LOOP
      IF v (x.item_name) IS NOT NULL THEN
        apex_util.set_session_state (x.item_name_sec, pkg_apex_sec.f_get_sec_val (v (x.item_name)));
      END IF;
    END LOOP;
  END sp_set_page_sec_values;
END pkg_apex_sec;
</pre>

<span style="font-weight:bold;">Step 2- Create Application Processes</span>

Create Application Processes (On Load Before Header) called: AP_SET_SEC_PAGE_ITEMS. This will allow you to pass IDs in the URL as you normally would.
<pre class="brush: sql">
BEGIN
  pkg_apex_sec.sp_set_page_sec_values;
END;
</pre>

Create Application Processes (On Submit and Before Computation ) called: AP_SET_UNSEC_PAGE_ITEMS
<pre class="brush: sql">
BEGIN
  pkg_apex_sec.sp_set_page_unsec_values;
END;
</pre>

Create Application Process (On Demand) called AP_NULL to set values in session (using AJAX).
<pre class="brush: sql">
BEGIN
  pkg_apex_sec.sp_set_page_unsec_values; -- Update the unsec values from secure values for the current page
  NULL;
END;
</pre>

<span style="font-weight:bold;">Step 3 - Create page with Interactive Report (IR)</span>

<pre class="brush: sql">
SELECT *
  FROM emp
 WHERE empno = NVL (:p1500_empno, empno)
</pre>

<span style="font-weight:bold;">Step 4 - Create "Secure" Id LOV and a "Hidden and Protected" unsecured fields</span>

Create P1500_EMPNO as Hidden and Protected. It's extremely important that you make the field Hidden and Protected so users can't alter the value in the form.
Create P1500_EMPNO_SEC as a Select List. LOV:

Instead of:
<pre class="brush: sql">
SELECT e.ename d,
       e.empno r
  FROM emp e
</pre>  

Use: 
<pre class="brush: sql">
SELECT e.ename d,
       pkg_apex_sec.f_get_sec_val (e.empno) r
  FROM emp e
</pre>  

<span style="font-style:italic;color:red">Besides adding the application processes etc, this is the only significant change that you'll need to make to your application</span>

At this point your application is good to go. Steps 5 and 6 are included only for demonstration purposes.

<span style="font-weight:bold;">Step 5 - JavaScript</span>
This JavaScript will be used to simulate the onChange event for a select list

<pre class="brush: js">
function onLovChange(pThisId){
  var get = new htmldb_Get(null,$v('pFlowId'),'APPLICATION_PROCESS=AP_NULL',$v('pFlowStepId'));  
  get.add(pThisId, $v(pThisId));
  vReturn = get.get();  
}
</pre>

<span style="font-weight:bold;">Step 6 - Button </span>
Create a Submit button that will submit and branch to the same page. This will demonstrate a "normal" submit process
Create a button called "AJAX". URL = javascript:onLovChange('P1500_EMPNO_SEC');

I haven't used this in production yet so their may be some changes that I add to the code. If you have any feedback please leave a comment.