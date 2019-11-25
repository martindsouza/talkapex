---
title: Case Insensitive Sorting in APEX
tags:
  - apex
date: 2019-02-24 22:16:47
alias:
---


By default Oracle does case sensitive sorting, which means that ascending order goes from `A-Z` then `a-z`. This is reflected in APEX when creating reports and sorting on them.

{% asset_img before.png %}

They're various workarounds to enable case insensitive sorting. The simplest is to change the entire application by modifying the following setting in `Shared Components > Globalization > Character Value Comparison` to `BINARY_CI`:

{% asset_img settings.png %}

Once set, all your reports will be sorted case insensitive. The first report now looks like this:

{% asset_img after.png %}

_Thanks to [Jorge Rimblas](https://twitter.com/rimblas) for showing me this!_
