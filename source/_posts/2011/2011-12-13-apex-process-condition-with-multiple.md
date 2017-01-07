---
title: APEX Process Condition with Multiple Buttons
tags:
  - apex
date: 2011-12-13 07:00:00
alias:
---

For new APEX developers, adding a page process condition can be a bit confusing at first when basing it on multiple buttons. This post will go through a scenario on how to easily use multiple buttons in a page condition.

**<span style="font-size: small;">The Setup</span>**
A classic example of having multiple buttons on a page is when you want to save or update a record. The Save button should only appear for new records, whereas the Update button should only appear for existing records. The catch is that when saving and updating the page you may need to run the same process.

The following setup highlights this classic example. Create a region in a page with two fields: P1_X and P1_Y. Then create three buttons: Save, Update, and Cancel. All three buttons should submit the page and an example of the page is shown below

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-Th7GcS6UnvY/TtpJnsNnnqI/AAAAAAAAEFQ/yTphEesUKKo/s1600/process_condition_setup.png)](http://4.bp.blogspot.com/-Th7GcS6UnvY/TtpJnsNnnqI/AAAAAAAAEFQ/yTphEesUKKo/s1600/process_condition_setup.png)</div>
Create the following page process:
Name: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">Update P1_Y</span>
Process Point: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">On Submit - After Computations and Validations</span>
Process: <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">:P1_Y := :P1_X * 2;</span>

**The Problem**
You'd like this process to run when both the Save and Update buttons are clicked. If you scroll down to the process's conditions area you'll notice that it has an option to restrict the process when a particular button is clicked (shown below).

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-jnbkgSKISeE/TtpJnS7sy6I/AAAAAAAAEE8/UNvhZ31djzg/s1600/process_condition_condition.png)](http://4.bp.blogspot.com/-jnbkgSKISeE/TtpJnS7sy6I/AAAAAAAAEE8/UNvhZ31djzg/s1600/process_condition_condition.png)</div>
As you can see you can only select one button from the list. At first glance the only option that you have to resolve the two button issue is to create a new process, which is a copy of the existing process, and set the condition to the other button. That doesn't necessarily make sense and can be an obvious maintenance nightmear.

The good news is that there is a very simple way to get around this issue. When you click a button it actually sets the [REQUEST](http://docs.oracle.com/cd/E23903_01/doc/doc.41/e21674/concept_sub.htm#BEIFJJBC) variable to the button name. The button name is defined in the button. For example the Save button's request is actually SAVE (shown below).

<div class="separator" style="clear: both; text-align: center;">[![](http://4.bp.blogspot.com/-NAhzVX9cpf0/TtpJn8-vFYI/AAAAAAAAEFM/kJn-_bwXs-Q/s1600/process_condition_button.png)](http://4.bp.blogspot.com/-NAhzVX9cpf0/TtpJn8-vFYI/AAAAAAAAEFM/kJn-_bwXs-Q/s1600/process_condition_button.png)</div>
Going back to the original problem, and leveraging what we know about the REQUEST variable, you can modify the process's condition to have it run when either the Save or Update buttons are selected as shown below.

<div class="separator" style="clear: both; text-align: center;">[![](http://3.bp.blogspot.com/-M_w6hjuT-EQ/TtpJngA96WI/AAAAAAAAEFA/PZTYRg5XwX4/s1600/process_condition_condition_good.png)](http://3.bp.blogspot.com/-M_w6hjuT-EQ/TtpJngA96WI/AAAAAAAAEFA/PZTYRg5XwX4/s1600/process_condition_condition_good.png)</div>
_Note: They're other ways to use the REQUEST variable in the conditions section. This one highlights a very simple use._
