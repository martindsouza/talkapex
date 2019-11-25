---
title: How to Reference JavsScript and CSS Files for Entire Application
tags:
  - apex
  - apex-5
  - javascript
  - css
date: 2017-01-04 21:08:27
alias:
---


[Kim Mertens](https://twitter.com/Dwarcake) recently posted the following question today on [Twitter](https://twitter.com/Dwarcake/status/816688777737408512): _Where to put JS-file that has to be loaded on every page in #orclapex 5.0? Tried "Global Page - Static region" but jQuery isn't loaded yet_

The answer isn't as straight forward as you think. Most people are used to referencing web files (JavaScript and CSS files) on `Page 0` from legacy APEX 4. As Kim hinted at in his question this isn't always the best thing to do.

In APEX 5 there's a specific area dedicated to reference custom web files. It's not the easiest thing to find at first (it takes me a few tries to find it sometimes if I haven't used it in a while).

{% asset_img js-storage.gif "" %}
