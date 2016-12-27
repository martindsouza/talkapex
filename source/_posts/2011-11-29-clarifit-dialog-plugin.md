---
title: ClariFit Dialog Plugin
tags: []
date: 2011-11-29 11:03:00
alias:
---

Over a year ago I released the [ClariFit Simple Modal](http://apex.oracle.com/pls/apex/f?p=45587:400:0) plugin. Since it's inception it's been downloaded almost 5000 times and has been used in several production applications (that I know of).

Today I'm please to release an "updated" version of plugin called [ClariFit Dialog](http://apex.oracle.com/pls/apex/f?p=45587:800:0::NO:::) plugin. Like the Simple Modal plugin, the Dialog plugin is a dynamic action and allows you to have a modal or dialog window. It also has a lot of new features (listed below). Going forward if you're going to use a modal/dialog window plug-in I recommend using the ClariFit Dialog plugin.

A demo is available on [plugins.clarifit.com](http://apex.oracle.com/pls/apex/f?p=45587:800:0) and it can be downloaded from [apex-plugin.com](http://apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/clarifit-dialog_152.html).

<table align="center" cellpadding="0" cellspacing="0" class="tr-caption-container" style="margin-left: auto; margin-right: auto; text-align: center;"><tbody><tr><td style="text-align: center;">[![](http://1.bp.blogspot.com/-AuQ8Zqz0vB0/TtGsh5-MX9I/AAAAAAAAED0/rXO_-mv-A-A/s1600/dialog_plugin_settings.jpg)](http://1.bp.blogspot.com/-AuQ8Zqz0vB0/TtGsh5-MX9I/AAAAAAAAED0/rXO_-mv-A-A/s1600/dialog_plugin_settings.jpg)</td></tr><tr><td class="tr-caption" style="text-align: center;">ClariFit Dialog Plugin Settings</td></tr></tbody></table>List of features:

*   Uses the jQuery UI dialog code and leverages the jQuery UI theme currently incorporated in your APEX application.*   Support for open/close directly in same plugin.
*   Option to have either a modal or dialog window.
*   Hide affected elements on page load. If set, this option will hide the region after the page is loaded. This removes the need to have separate region templates.
*   Chose visible state of the modal window after it's closed.