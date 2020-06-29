---
title: How to Easily Reload Modal Pages in APEX
tags:
  - apex
date: 2020-03-12 07:00:00
alias:
---


If you've created a modal page in APEX you've inevitably run into the issue of needing to refresh a modal page after changing it. You can't use the normal browser's refresh button or use the keyboard shortcut `ctrl/cmd + r` as it refreshes the underlying page, not just the modal page. Instead you either need to close and reopen the modal page or right click and select the reload frame option as shown below:

{% asset_img rclick-reload.png %}

Instead of struggling with the reload situation I usually create a "Modal Reload" button that will show up in all modal pages and only work in development environments. Here's how to add one:

On `Page 0`, add a region called `DEV_ONLY Modal`. The region will be automatically included on modal pages.

- `Appearance > Template: Blank with Attributes`
- `Configuration > Build Option: DEV_ONLY`
  - This assumes you have a [Build Option](http://www.grassroots-oracle.com/2010/06/oracle-apex-build-options.html) called `DEV_ONLY`. If not it's fairly easy to create.
- `Server-side Condition > Type: Rows Returned`. `SQL Query`:

```sql
select 1
from apex_application_pages aap
where 1=1
  and aap.application_id = :app_id
  and aap.page_id = :app_page_id
  and lower(aap.page_mode) = 'modal dialog'
```
  
Add a button to this region called `RELOAD_MODAL`:

- `Button Name: RELOAD_MODAL`
- Behavior
  - `Action: Redirect to URL`
  - Target
    - `Type: URL`
    - `URL: javascript:location.reload();`
- Optional (that I used for this demo)
  - `Button Template: Text with Icon`
  - `Template Options > Type: Warning`
  - `Icon: fa-refresh`


Demo of the Reload button in action

{% asset_img reaload-button.gif %}


**Update** (moving the button to the button bar)

The screenshots and demo above all show the Reload button at the top of the page. This changes the UI behavior in dev. To move it to the modal button bar, on Page 0 do the following:

- Add a new DA: `DEV_ONLY: onPageLoad`
  - Event: Page Load
  - Action: Execute JavaScript Code
    - Affected Elements
      - Selection Type: `Button`
      - Button: `RELOAD_MODAL`
      - JavaScript Code:

```javascript
// Bottom left button container
var btnContainer = $('.t-Dialog-footer').find('.t-ButtonRegion-col--left > .t-ButtonRegion-buttons');

// Move to button container
this.affectedElements.appendTo(btnContainer);
```