---
title: Upgrading to Nice Switch in APEX 19
tags:
  - apex
  - apex-19
date: 2020-02-15 07:35:07
alias:
---


When upgrading an application to APEX 19.x, if you have a Switch page item it probably still looks like the old "Yes/No" boxes:

{% asset_img old-swtich.png %}

To change to the new, more modern, Switch item you need to change this for the entire application. Do to so go to `Shared Components > Component Settings > Switch`. From there set the `Display Style` to `Switch`. All the Switch items will now look like:

{% asset_img new-switch.png %}

For all new applications created in 19.x the Display Style is already Switch so there is nothing to do in order to have this type of Switch.