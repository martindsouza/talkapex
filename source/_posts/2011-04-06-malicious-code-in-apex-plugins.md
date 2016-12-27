---
title: Malicious Code in APEX Plugins
tags:
  - ORACLE APEX
date: 2011-04-06 07:00:00
alias:
---

<span style="font-style:italic;color:red;">The following post could be taken in the wrong way which may discourage some people from using plugins developed by the APEX community. Please read the <span style="font-weight:bold;">ENTIRE</span> post before you make any conclusions.</span>

<span style="font-style:italic;">They're some good comments for this post. I've summarized them and included my thoughts in the following post: [Malicious Code in APEX Plugins - Feedback](http://www.talkapex.com/2011/04/malicious-code-in-apex-plugins-feedback.html)</span>

One of the best features of APEX plugins is the ability to share them with the community. Some sites, such as [apex-plugin.com](http://www.apex-plugin.com/), host over 70 plugins. Most of these plugins are open source and free to use in production applications. These plugins have saved organizations lots of time developing redundant code.

There is the possibility for someone to develop a malicious plugin which could compromise your entire database or access to your application. For example I could easily create a dynamic action plugin that could send me a list of all your tables each time the plug-in is run. That being said I would never do that, but someone with bad intentions could.

What does this mean for you? Before integrating a plugin into your application (even in a development environment) you should look at both the PL/SQL and JavaScript code to see what they do. In most cases this extra step will take a few minutes but can save you a lot of headaches (and potentially your job). This is one of the main reasons why I don't compress JavaScript files in plugins that I post on [apex-plugin.com](http://www.apex-plugin.com/my-plugins/user/clarifit.html). I want to ensure that other developers can easily review my code. 

I am not trying to fear monger (there's enough of that going on in society as is) developers about the threat of using plugins. I just want to make sure that developers know the risks involved and what they should do to protect their database and business when integrating 3rd party plugins.