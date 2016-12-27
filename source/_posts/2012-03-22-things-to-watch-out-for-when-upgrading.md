---
title: Things to Watch Out for When Upgrading an APEX Plugin
tags:
  - apex-plugin
date: 2012-03-22 08:00:00
alias:
---

Now that APEX plugins have been around for a while I'm starting to notice that upgrades are being released for the same plugin. This is a natural occurrence in the lifecycle of software development and is expected.

Prior to upgrading an APEX plugin you should be aware of what will happen to the settings (also known as Custom Attributes) for your plugin before upgrading. Before I continue it's important to note the two different types of custom attributes a plugin can have.

**Application:** These attributes are applicable for the plugin as a whole across the entire application. Think of them as a global variable for the plugin

**Component**: These attributes are applicable for each instantiation of the plugin. For example, if the plugin is an item type plugin the settings found while editing the item are component level attributes.

When upgrading a plugin the component level attributes/settings will retain their value (this is good). The same is not true for application level attributes. They will be reset to the plugin's default values. For example if I configure the [ClariFit Dialog](http://apex.oracle.com/pls/apex/f?p=45587:800:0::NO) plugin they're a few settings I can change: Background Color and Background Opacity. In the image below I've changed them from the default values to _red_ and _50%_ respectively.

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-AbE8XEgHwKY/T2jQjAOLkdI/AAAAAAAAEHE/3PfGP5g96Bw/s400/plugin_before_upgrade.png)](http://2.bp.blogspot.com/-AbE8XEgHwKY/T2jQjAOLkdI/AAAAAAAAEHE/3PfGP5g96Bw/s1600/plugin_before_upgrade.png)</div>
After I upgrade the plugin to a newer version the application level attributes are reset back to the plugin's default values shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://2.bp.blogspot.com/-2B3zMtCmzvk/T2jQi2qnHhI/AAAAAAAAEHA/DNr2-opaFyg/s1600/plugin_after_upgrade.png)](http://2.bp.blogspot.com/-2B3zMtCmzvk/T2jQi2qnHhI/AAAAAAAAEHA/DNr2-opaFyg/s1600/plugin_after_upgrade.png)</div>
Be sure to take note of your plugin application level settings before updating a plugin. I find the easiest way to do this is to take a screen shot, similar to the images above, before I upgrade the plugin and compare the values with the new default values and make the appropriate changes.
