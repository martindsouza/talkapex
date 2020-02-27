---
title: APEX IR Saved Report Aliases
tags:
  - apex
  - interactive-report
date: 2020-02-15 14:36:15
alias:
---

In APEX you can save Interactive Reports (IR) default reports as both Primary and Alternate. Both of these types of default reports are available to all users. To save an alternate report, click the `Actions` button then `Report > Save Report` (*Note you must be logged into the APEX developer to save default reports*). Select the option to save `As Default Report Settings` and the save screen will look like below:

{% asset_img ir-save-report.png %}

In the APEX developer you can now see the saved report (`Managers` in this case) and modify its alias. By default a random number will be assign as the alias. You can modify this alias name.

{% asset_img ir-report-attributes.png %}

You can then use the alias to link to a saved report anywhere in your application.

The following query will get a list of all the default reports, their aliases, and a report link for a given page:

```sql
select 
  pr.region_name, 
  nvl(pir.report_name, 'Primary') report_name, 
  pir.report_alias, 
  pir.report_link_example
from dual
  join apex_application_page_ir_rpt pir on 1=1
  join apex_application_page_regions pr on 1=1
    and pr.application_id = pir.application_id
    and pr.page_id = pir.page_id
    and pr.region_id = pir.region_id
where 1=1
  and pir.application_id = :app_id
  and pir.page_id = :app_page_id
  and pir.status = 'PUBLIC'
;

REGION_NAME REPORT_NAME    REPORT_ALIAS  REPORT_LINK_EXAMPLE
___________ ______________ _____________ __________________________________________
Report 1    Primary        167050        f?p=&APP_ID.:3:&APP_SESSION.:IR_167050
Report 1    Sal GTE 2000   180055        f?p=&APP_ID.:3:&APP_SESSION.:IR_180055
Report 1    Managers       MANAGERS      f?p=&APP_ID.:3:&APP_SESSION.:IR_MANAGERS
```


