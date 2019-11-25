---
title: How to Save Page Data but Show Errors in APEX
tags:
  - apex-5.1
date: 2018-03-29 08:15:48
alias:
---


In most cases error messages are shown on an APEX page when the user submits a form and a validation fails. Sometimes (not often) you may need to manually control the validations rather than using the declarative validations section. To do this you can use the [`apex_error`](https://docs.oracle.com/database/apex-5.1/AEAPI/APEX_ERROR.htm#AEAPI2209) API. The following is an example of how to call the API.

```sql
apex_error.add_error(
  p_message => 'Custom error from PL/SQL',
  p_display_location => apex_error.c_inline_in_notification);
```

This will generate the following on the page:

{% asset_img custom-error.png %}

`apex_error.add_error` triggers a rollback and in most cases this is a good thing. In some very specific cases you may not want a rollback to occur. The rest of this article covers how to generate errors without a rollback occurring.

## Edge Case

I recently had a rare edge case for a page submit and errors. The page was used to edit batch staging data. The requirements were:

- User submits page
- All the data is saved (in a staging table)
  - _Since this is staging area, the data must be saved and committed each time regardless of errors _
- Do validations against staged data
  - If errors were found, the page would show them (remember the data still needs to be saved)
  - If no errors were found, the staged data would be posted and then branch to another page.

## The Solution

Given that `apex_error.add_error` performs a rollback the simple solution is to save the staged data, perform an explicit `commit`, and then do custom validations. Unfortunately this was not possible due to additional requirements that I left out of this article.

_The following relies on the fact that you know about the `apex.message` API. If you do not please read {% post_link custom-apex-notification-messages this article %}._


This is an overview of the final solution:

- On Submit PL/SQL page process that:
  - Saved all the page's data
  - Called a custom validation function that returned a JSON object. If no errors were found this function would return `null`. The results of this function were stored in the page item `P1_ERR_JSON`.
- Created an `onPageLoad` Dynamic Action to display errors using the `apex.message` API

The following pseudo code summarizes the above description:

### Page Process

```sql
-- ...
-- Save staging data
-- ...
-- Validations:
l_err_json := null;

select sum(salary)
into l_salary_total
from my_stage_table;

if l_salary_total > 1000 then
  -- Produce JSON error like:
  -- {
  --   "errMsg": "Total salary must be less than $1000"
  -- }
  apex_json.initialize_clob_output;
  apex_json.open_object;
  apex_json.write('errMsg', 'Total salary must be less than $1000');
  apex_json.close_object;

  l_err_json := apex_json.get_clob_output;
  apex_json.free_output;
end if;

apex_util.set_session_state('P1_ERR_JSON', l_err_json, false);
```

### `onPageLoad` Dynamic Action

```js
err = apex.item('P1_ERR_JSON').getValue();

if (err.length > 0) {
  err = JSON.parse(err);

  apex.message.clearErrors();
  apex.message.showErrors([
    {
      type: apex.message.TYPE.ERROR,
      location: ["page"],
      message: err.errMsg,
      unsafe: false
    }
  ]);
}// end if
```

When an error occurred, the page looked like:

{% asset_img total-error.png %}

## Wrapping Up

This solution is a summary of the final code. You can easily expand it to handle multiple error messages, associate with page items, etc.