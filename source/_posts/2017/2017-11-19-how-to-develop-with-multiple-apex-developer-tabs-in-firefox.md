---
title: How to Develop with Multiple APEX Developer Tabs in Firefox
tags:
  - apex
date: 2017-11-19 20:35:42
alias:
---


Eventually all APEX developers need to have multiple versions of the development environment open at the same time. A simple example of this is if you're working with two pages (Page 1 and Page 2) that are dependant on one another. You constantly have to toggle between them as you can't have two tabs open for development (one for P1 and one for P2).

Most people either open a different browser or open a new browser tab in Private or Incognito mode. Now [Firefox](https://www.mozilla.org/) allows you to get around this issue by having [Multi-Account Containers](https://blog.mozilla.org/firefox/introducing-firefox-multi-account-containers/). 

{% asset_img firefox-multi-account-containers.png %}

Multi-Account Containers segregate your cookies by containers. Aside from APEX development you can also have multiple instances of Gmail or Office 365 opened in different container tabs.

For now you must install the [Multi-Account Containers Plugin](https://addons.mozilla.org/en-US/firefox/addon/multi-account-containers/?src=userprofile) (built by Mozilla) as it's not included by default. Note I use the plugin on [Firefox Developer Edition](https://www.mozilla.org/en-US/firefox/developer/) with no issues.