---
title: Custom Calendar CSS in APEX
tags:
  - apex
  - css
date: 2017-02-18 19:11:20
alias:
---


The new (_not legacy calendar_) in APEX 5 comes with a set of default calendar CSS classes to style calendar events. The following screenshot is from the inline help for the calendar `CSS Class` attribute:

{% asset_img calendar-help.png "" %}

I couldn't find how to create a custom class in the documents (as the help suggests). Using the pre-defined classes as an example I was able to create a custom class for a new color for my calendar. The following is an example of a custom APEX calendar class:

```css
.fc .fc-event.apex-cal-demo {
  background-color: #F0F8FF;
  border-color: #000000;
  color: #FFFFFF;
}
```

In the calendar query this class can now be referenced as `apex-cal-demo`.
