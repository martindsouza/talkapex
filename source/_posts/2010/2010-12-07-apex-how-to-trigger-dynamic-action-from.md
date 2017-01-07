---
title: 'APEX: How to Trigger Dynamic Action From Button'
tags:
  - apex
date: 2010-12-07 20:32:00
alias:
---

Someone recently asked me how to trigger a dynamic action from a button. At first I thought this was trivial question until I tried to do it and found it's not as straight forward as I expected.

The following steps cover how to create a dynamic action on a button:

<span style="font-weight:bold;">Modify Button Template:</span>

You'll need to modify the button template to allow for the button attributes to be applied to the button. This will allow us to use an ID to identify a button. To apply the button attributes go to Shared Components > Templates > Select the default button template (for most instances called "Button"). Add #BUTTON_ATTRIBUTES# to the button tag in the Template section. For example from:

&lt;button value="#LABEL#" onclick="#LINK#" class="button-gray" type="button"&gt;

To:

&lt;button value="#LABEL#" onclick="#LINK#" class="button-gray" type="button" <span style="font-weight:bold;">#BUTTON_ATTRIBUTES#</span>&gt;

<span style="font-weight:bold;">Create Button</span>
On the page you're working on create a Region Button:

<span style="font-style:italic;">Button Name</span>: TEST BUTTON
<span style="font-style:italic;">Label</span>: Test Button
<span style="font-style:italic;">Button Style</span>: Template Based Button
<span style="font-style:italic;">Button Template</span>: Button (or what ever button template you modified)
<span style="font-style:italic;">Button Attributes</span>: id="test_button"
<span style="font-style:italic;">Make sure the id is unique</span>

<span style="font-style:italic;">Action</span>: Redirect to URL
<span style="font-style:italic;">URL Target</span>: javascript:return;

<span style="font-weight:bold;">Create Dynamic Action</span>
Create a Dynamic Action. When you get to the "When" section:

<span style="font-style:italic;">Event</span>: Click
<span style="font-style:italic;">Selection Type</span>: DOM Object
<span style="font-style:italic;">DOM Object</span>: test_button
<span style="font-style:italic;">The DOM object represents the ID that you defined while creating the button.</span>
<span style="font-style:italic;">Condition</span>: No Condition

Create your True and/or False actions accordingly.

Now when you run the page and click the button it should execute your dynamic action.
