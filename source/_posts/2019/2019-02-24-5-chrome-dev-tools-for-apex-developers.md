---
title: 5 Chrome Dev Tools For APEX Developers
tags:
  - apex
  - chrome
date: 2019-02-24 18:00:32
alias:
---


[Google Chrome](https://www.google.com/chrome/) has a lot of great [DevTools](https://developers.google.com/web/tools/chrome-devtools/). Here are 5 tools that may help with your APEX development.

# Live Expressions

If you had a hidden APEX page item that you wanted to monitor you constantly had to go into the console and type in write out `console.log(...)` to view its value. This can be annoying if the value will change multiple times as you test the page. [Live Expressions](https://developers.google.com/web/updates/2018/08/devtools#watch) (introduced in Chrome 70) resolves this issue by being able to track output by updating the value every 250 milliseconds.

{% asset_img live-expressions.gif %}

The nice thing is that Live Expressions are preserved when you refresh a page. It's hard to tell in the example above but I refreshed the page at one point.

# Command Menu

Chrome DevTools have a lot of settings and features that you may not know about, let alone know where to access. Thankfully it has the Command Menu which is like Spotlight in MacOS. Anywhere in the DevTools type `Cmd+Shift+P` (Windows users `Ctrl+Shift+P`) to enable them. These shortcut keys are the same in [Visual Studio Code](https://code.visualstudio.com/) so developers that use VSC will be very familiar with the behavior.

{% asset_img command-menu.gif %}

In the last part of the above demo I typed in `Cmd+P` (Windows users `Ctrl+P`). This will open a file that the webpage loaded or that you have in your workspace (shown below)

{% asset_img command-menu-workspace.png %}


# Dark Mode

A long time ago I changed my editor to a dark theme. Since then a lot of applications (including APEX 19.1 and MacOS Mojave) support dark themes. Chrome DevTools now supports dark mode.

{% asset_img dark-mode.gif %}

# Persist CSS Changes

A lot of times when doing basic UI / CSS debugging you may inspect an element and modify its CSS properties. When you refresh the page you lose all your changes. This is especially frustrating if doing live-CSS edits with end users to do quick proof of concepts.

You can persist CSS changes using the Chrome Overrides feature. To enable this go to `Sources > Overrides > +` to add a folder. This can be any empty folder you want.

{% asset_img overrides-config.png %}

When you do this you'll get the following warning message. Be sure to `Allow` Chrome to access the folder. You'll only need to do this setup once.

{% asset_img overrides-permission.png %}

Now when you modify a CSS property and refresh the page it'll persist even after you reload the page:

{% asset_img overrides-demo.gif %}

If you want to quickly reset all the changes you've made just delete everything in your `overrides` folder.

# Adding Breakpoints in Code

They're various ways to add [breakpoints](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints) to help you debug. Sometimes you know exactly where in you JS code you want to add breakpoints but don't want to manage it via the UI. In this case you can add breakpoints directly in your code by adding the `debugger;` command.

{% asset_img breakpoints.gif %}

# Offline Fun

This won't exactly help you with your APEX development, nor is it a DevTool feature. If you're ever offline and really bored Chrome's got you covered. Simply hit the `spacebar` when you see the dinosaur to start and use the `up` and `down` arrows to move it around.

{% asset_img chrome-fun.gif %}
