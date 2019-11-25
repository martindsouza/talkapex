---
title: Oracle SQL Developer Randomly Closes
tags:
  - sql-developer
  - sql-data-modeler
date: 2017-07-21 22:59:33
alias:
---


I recently had a situation that running either [Oracle SQL Developer](http://www.oracle.com/technetwork/developer-tools/sql-developer/overview/index.html) or [SQL Developer Data Modeler](http://www.oracle.com/technetwork/developer-tools/datamodeler/overview/index.html) on a Windows 7 VM (more on this later) would randomly close (or crash depending on how you look at it). The log files didn't produce anything substantial and after a lot of digging around it was due to the JVM and the video driver. [Jeff Smith](https://twitter.com/thatjeffsmith) confirmed this over Twitter:

<blockquote class="twitter-tweet" data-conversation="none" data-lang="en"><p lang="en" dir="ltr">So it&#39;s the jvm crashing because of the video driver...happens on Windows a decent amount, usually fixed with a driver update</p>&mdash; Jeff Smith 🥃 ☜ (@thatjeffsmith) <a href="https://twitter.com/thatjeffsmith/status/888428472326029315">July 21, 2017</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

I was using windows on a VM and it had no available video driver updates. After reading [this post](https://stackoverflow.com/questions/40651162/sql-developer-crashes-randomly-every-time) on StackOverflow I found there is a configuration setting to get around the issue. It wasn't very clear as to where to make the configuration change so here it is:

1. Modify `datamodeler\ide\bin\jdk.conf`
2. Add `AddVMOption  -Dsun.awt.nopixfmt=true` on a new line after `AddVMOption  -Dsun.java2d.noddraw=true`
3. Restart the application

A few additional notes:

1. The versions I downloaded included the JDK. If you're using your own JDK then the location of `jdk.conf` will probably be different.
2. If you're using the included JDK version then you'll need to remember to do this step with each update of the applications.
