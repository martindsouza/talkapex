---
title: APEX Shortcuts
tags:
  - apex
date: 2014-02-24 07:00:00
alias:
---

The other day I was dabbling around in APEX and noticed a link for Shortcuts in the Shared Components section.

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-sIw9ijxdpkc/UwqTbQS1blI/AAAAAAAAEmw/TQgAEA03enU/s1600/apex_shortcuts.png)](http://4.bp.blogspot.com/-sIw9ijxdpkc/UwqTbQS1blI/AAAAAAAAEmw/TQgAEA03enU/s1600/apex_shortcuts.png)</div>

I’ve never used Shortcuts before (let along knew about them) so I tried it out. To start here’s how Shortcuts are described (as copied from APEX screen):

_Shortcuts are a repository of shared static or dynamic HTML. Shortcuts are substitution strings that are expanded using the syntax: `"SHORTCUT_NAME"`. Shortcuts are used in the following locations:_

* Region Source for regions of type HTML_WITH_SHORTCUTS
* Region Templates, Region Headers & Footers
* Item Labels
* Item Default Value
* Item Post Element Text
* Item Help Text
* HTML Header of a page

Creating shortcuts on page item labels and page item post element text attributes can include the following substitution strings:
* `#CURRENT_FORM_ELEMENT#`
* `#CURRENT_ITEM_ID#`
* `#CURRENT_ITEM_NAME#`
* `#CURRENT_ITEM_HELP_TEXT#`

To reference Shortcuts you need to use the `“shortcutname”` syntax (quotes included). Since they are wrapped in quotes and could conflict with regular text I strong recommend using a naming scheme such as `SC_NAME`.

_Note: I previously wrote an article about the different ways to reference APEX variables. I have updated it to include Shortcuts. The article is available [here](http://www.talkapex.com/2011/01/variables-in-apex.html)._

Shortcuts can either be statically defined or reference a PL/SQL function. It’s important to note that if you do call a PL/SQL function it will execute the code each time the Shortcut is referenced. For example, if you have the same Shortcut in three different regions on a page it’ll call the function three times. This may be a good or bad thing depending on how you use it.

Though I haven’t found an immediate need for Shortcuts I think there could be some situations where it can come in handy for labels and templates especially since it allows you to reference a function which can dynamically generate content.
