---
title: 'APEX Plugin: Simple Modal'
tags:
  - APEX
  - APEX PLUGIN
  - ORACLE APEX
date: 2010-08-20 09:00:00
alias:
---

I just published another plugin called Simple Modal: [http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html](http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html)

This plugin allows you to use any region(s) (or DOM object) in your APEX application as a modal window. 

When developing this plugin I learned a few more things that may be useful when developing plugins:

- <span style="font-weight:bold;">Scope Creep:</span> When developing a plugin you can make it do a lot of things. This may lead you to try and include extra unnecessary functionality. Try to remember the goal you're trying to achieve, or more importantly, what you're developers will try to achieve with the plugin. 

- <span style="font-weight:bold;">Instrumentation</span>: I'm a big fan of [Tom Kyte](http://tkyte.blogspot.com) and really like the emphasis he puts on [Code Instrumentation](http://tkyte.blogspot.com/2005/06/instrumentation.html). You may want to add some debug information in your JavaScript code to help you understand what is going on. As part of this plugin I included a logger package which is essentially a wrapper for <span style="font-style:italic;">console</span> but will work in all browsers. I'll write about this in another post and include the final copy for general use.