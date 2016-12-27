---
title: 'APEX Plugin: Syntax Highlighter'
tags:
  - APEX
  - APEX PLUGIN
  - ORACLE APEX
date: 2010-07-16 09:00:00
alias:
---

I finally got around to developing my first APEX Plugin! 

To summarize this plugin is a code syntax highlighter based on [http://alexgorbatchev.com/SyntaxHighlighter/](http://alexgorbatchev.com/SyntaxHighlighter/).
. This is the same syntax highlighter for code samples on this site. You can download it here: [http://www.apex-plugin.com/oracle-apex-plugins/item-plugin/syntax-highlighter.html](http://www.apex-plugin.com/oracle-apex-plugins/item-plugin/syntax-highlighter.html). 

[![](http://2.bp.blogspot.com/_33EF80fk9sM/TD_pP31RMWI/AAAAAAAADxo/kWU0d3xwmzw/s400/syntax_highlighter.jpg)](http://2.bp.blogspot.com/_33EF80fk9sM/TD_pP31RMWI/AAAAAAAADxo/kWU0d3xwmzw/s1600/syntax_highlighter.jpg)
Since this was my first APEX plugin that I created I thought I'd give some pointers to help others writing their first plugin:

- <span style="font-weight:bold;">Decide on a feature you want to add</span>. This can be harder said than done. If you're new to JavaScript etc, then you may want to create an example in a html file so that you can reference it later on.

- <span style="font-weight:bold;">Keep it simple</span>. Chose a simple feature to add. If you decide to add something very complex you may just get lost.

- <span style="font-weight:bold;">Put your PL/SQL code in a package/function</span> so that you can develop using a 3rd party tool such as Toad or SQL Developer. This will allow you to quickly debug your code. Once it's working you can extract it and put it inline with your plugin

- <span style="font-weight:bold;">Look at other examples</span>. View other plugins and see how they were built etc.