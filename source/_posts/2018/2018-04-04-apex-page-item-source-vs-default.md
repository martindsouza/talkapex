---
title: APEX Page Item Source vs Default
tags:
  - apex
date: 2018-04-04 22:50:45
alias:
---


Someone recently asked me about the difference between an APEX page item's `Source`  and `Default` attributes. I've always been confused about these options so I decided to further investigate them. This post will cover the differences between Source and Default along with some additional info.

{% asset_img source-default.png %}

## Order of Operations

Page items can be set in different ways and there is an order of operations to which "setting" takes precedence:

1. **Session State**: What ever is in session state will always trump the page item's Source or Default settings_*_
1. **Source**: Source comes next. For most people this is `null` or `Database Column` (if using `Automatic Row Fetch`)
1. **Default**: If the page item is still null then Default will be used. This attribute doesn't have as many options as Source (just `Static Value`, `PL/SQL Expression`, and `PL/SQL Function Body`)

_* The Source attribute has an option to ignore the current session state value._

## Session State vs On Screen

Neither Source or Default saves the value to session state_*_. Instead it is what is shown on screen. If Source or Default is used the value will only be saved to session state once the page is submitted.

_* Read below as the value is temporarily used in session state for the remaining duration of the page load._

## Additional Info

### Is Default always Necessary?

I think a lot of the confusion with Source and Default is that in some cases Default is redundant and unnecessary. I think Default is only required when the Source is set to `Database Column`, `Item`, or `Preference`. For the other options (see below) you can wrap the returned value with a `nvl`.

{% asset_img source-types.png %}

### Source `Used` Attribute

Source has additional attribute that Default does not: `Used`

{% asset_img source-used.png %}

If the `used` option is set to `Always, replacing any existing value in session state` this means that after the page has loaded the value in Session State may have a value that is different than the value that is shown on the screen. The following image shows the result of this situation (i.e. different session state vs on screen value).

{% asset_img session-source-diff.png %}

### Temporary Change of Session State

Suppose that a `Before Header` computation exists and sets `P1_DEMO` to a static value: `From Computation`. This means that during page load, right after the computation is run, the session state value for `P1_DEMO` is `From Computation`. 

`P1_DEMO` Source is set to `From Source` and `Always, replacing any existing value in session state` is used. After `P1_DEMO` is "loaded" during the page load computation, the session state of `P1_DEMO` is `From Source` for the duration of the page load. After the page is loaded the session state value is `From Computation`. The following image highlights this. For both the `Before` and `After` display only items the Source was set to `&P1_DEMO.`

{% asset_img session-state-temp-change.png %}

This can cause some weird behaviors on a page that has AJAX calls. The value in session state changes during the page load process. After the page is loaded it may flip flop and the AJAX call could use the "old" session state value (see screen shot above).

