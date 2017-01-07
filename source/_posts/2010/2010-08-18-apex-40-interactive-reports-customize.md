---
title: APEX 4.0 Interactive Reports - Customize Wait Display
tags:
  - apex
  - apex-plugin
date: 2010-08-18 22:37:00
alias:
---

Over a year ago I wrote about how to customize the APEX IR wait logo ([http://www.talkapex.com/2009/04/apex-interactive-reports-customize-wait_28.html](http://www.talkapex.com/2009/04/apex-interactive-reports-customize-wait_28.html)). If you read that post you'll notice it's quite lengthy and can be intimidating if you're new to JavaScript.

With APEX 4.0 this is a lot easier to do since they're plugins to declaratively add this functionality. This post will go over how to customize the APEX IR Wait logo in APEX 4.0\. You can try a demo here: [http://apex.oracle.com/pls/apex/f?p=20195:3200](http://apex.oracle.com/pls/apex/f?p=20195:3200)

<span style="font-weight:bold;">- Create an IR report region</span>
<pre class="brush: sql">
    SELECT e.*, SUM (e.sal) OVER () test
      FROM emp e
CONNECT BY LEVEL <= 5
</pre>
<span style="font-weight:bold;">- Install Plugin</span>

- Download the Simple Modal plugin: [http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html](http://www.apex-plugin.com/oracle-apex-plugins/dynamic-action-plugin/simple-modal.html)
- Shared Components / Plug-ins / Import
- They're 2 plugins included in the zip file. Import both of them (Show and Close)

<span style="font-weight:bold;">- Create Show Dynamic Action</span>

- RClick on the IR region and click "Create Dynamic Action:
- Advanced
- Name: Show IR Wait
- Next
- Event: Before Refresh
- Selection Type: DOM Object
- DOM Object: apexir_WORKSHEET_REGION
- <span style="font-style:italic;">Note: We're using the DOM object and not the region since we can port this example to Page 0 and it will apply to all your IRs</span>
- Next
- Action: Select Simple Modal - Show
- <span style="font-style:italic;">You can modify some of the plugin attributes here if you'd like</span>
- Next
- Selection Type: DOM Object
- DOM Object: apexir_LOADER
- Create

<span style="font-weight:bold;">- Create Close Dynamic Action</span>

- RClick on the IR region and click "Create Dynamic Action:
- Advanced
- Name: Close IR Wait
- Event: After Refresh
- Selection Type: DOM Object
- DOM Object: apexir_WORKSHEET_REGION
- Next
- Action: Select Simple Modal - Close
- Create

Now when you run the IR it'll make the screen modal while it's reloading the data. If you want to run on all IRs then you can add this dynamic action to Page 0.

<span style="font-style:italic;">If you run the [demo](http://apex.oracle.com/pls/apex/f?p=20195:3200) in a console-enabled browser (Firefox, Chrome, Safari) you'll notice that the plugin includes some additional debug information. I'll be posting the logging JavaScript package that was used in the plugin soon.</span>
