---
title: How to Reduce Width of IR Link Column in APEX
tags:
  - apex
  - interactive-report
date: 2020-02-14 19:13:47
alias:
---


Interactive Reports (IR) in APEX have a attribute to include a link column:

{% asset_img ir-attribute.png %}

When you run the page the link column takes up more space than necessary:

{% asset_img ir-link-column-default.png %}

You can reduce the width by adding the following CSS to your application: *Note width may vary depending on circumstances)* 

```css
th#LINK,
td[headers=LINK]
{
  width:40px;
}
```

Column links will now look like:

{% asset_img ir-link-column-reduce.png %}