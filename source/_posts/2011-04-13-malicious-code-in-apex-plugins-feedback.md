---
title: Malicious Code in APEX Plugins - Feedback
tags:
  - apex-plugin
  - security
date: 2011-04-13 05:24:00
alias:
---

My [previous post](http://www.talkapex.com/2011/04/malicious-code-in-apex-plugins.html) about [Malicious Code in APEX Plugins](http://www.talkapex.com/2011/04/malicious-code-in-apex-plugins.html) identified the possibility of harmful code in plugins <span style="font-style:italic;">(if you haven't read it please read the post before continuing)</span>. Several people had some excellent feedback which they included in the [comments section](http://www.talkapex.com/2011/04/malicious-code-in-apex-plugins.html#comments). This post summarizes their comments and provides my thoughts on them.

<span style="font-weight:bold;">Oracle Maintaining Plugin Repository
</span>
The idea is for Oracle to host something similar to Apple's App Store so that all code must pass a set of standards etc. I don't work for Oracle so I can't comment on this too much and I think it's a good idea. That being said the current community plugin repository, [apex-plugin.com](http://www.apex-plugin.com/), has an approval process before plugins are released.

<span style="font-weight:bold;">Wrapped PL/SQL and Licensed Plugins</span>

Some plugins have wrapped PL/SQL source code and obfuscated/minimized JavaScript (JS) code. Plugin developers may need to wrap their PL/SQL code since their plugins are licensed. They may also minimize their JS files for performance issues. When you use these plugins the company that developed it can help determine its legitimacy (more on this in the next section). Just because it's wrapped doesn't mean you should not install it. They're other ways to validate that it is safe to use.

<span style="font-weight:bold;">Trusted Developers and Organizations</span>

They're some companies and developers within the APEX community that are well known and trusted. These companies specialize in APEX and would never write malicious code. For example I would never hesitate to install a plugin from organizations such as (but not limited to) [ClariFit](http://www.clarifit.com/), [Apex Evangelists](http://www.apex-evangelists.com), [APEX Freelancer](http://www.theapexfreelancer.com), [Skill Builders](http://www.skillbuilders.com/), and [Sumneva](http://www.sumneva.com).  

<span style="font-weight:bold;">Scalability and Upgrades</span>

[Scott Spendolini](http://spendolini.blogspot.com/) made a great comment about the scalability of plugins and upgrades. I think this has to be examined on a case by case basis. If you're using a plugin on a small application that doesn't get a lot of hits then it may be a moot point. If your application gets millions of hits a day and you use a poorly optimized plugin then maybe you need to modify it to fit your needs. When looking at the source code you may not only be looking for malicious code but also techniques to improve performance for your specific needs. If the plugin has wrapped PL/SQL you can try to contact the developer/company to address your specific needs.

Like all software, plugins may need to be upgraded as APEX evolves (and it's 3rd party add-ons like [jQuery](http://jquery.com/)). If the plugin is open source you can easily modify the code or email the developer with a change request. I've had several people email me about bugs and feature enhancements for plugins and was able to implement them in future versions.
