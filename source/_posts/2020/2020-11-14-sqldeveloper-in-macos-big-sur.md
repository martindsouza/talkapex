---
title: SQLDeveloper in macOS Big Sur
tags:
  - sql-developer
  - macos
date: 2020-11-14 13:58:13
alias:
---


After upgrading to [macOS Big Sur](https://www.apple.com/ca/macos/big-sur/) I couldn't run [Oracle SQL Developer](https://www.oracle.com/database/technologies/appdev/sqldeveloper-landing.html). I got the following error: `The application “SQLDeveloper.app” can’t be opened`

{% asset_img macos-error.png %}

To resolve this issue I got help from [Kris Rice](https://twitter.com/krisrice) and [Niels de Bruijn](https://twitter.com/nielsdb). The issue is caused by Apple and the Java applet plugin. 

Here's how I got SQL Developer working:


## List your Java Versions

```bash
/usr/libexec/java_home -V

# This will give an output like:

Matching Java Virtual Machines (3):
    11.0.2 (x86_64) "Oracle Corporation" - "Java SE 11.0.2" /Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
    1.8.271.09 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_201 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
```

The one that caused the problem is the `"Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home` and you'll need to remove it.

## Remove the Applet Plugin

```bash
sudo rm -rf "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/"


# After deleting this folder it should be removed from the Java Home list
/usr/libexec/java_home -V

# Output
Matching Java Virtual Machines (2):
    11.0.2 (x86_64) "Oracle Corporation" - "Java SE 11.0.2" /Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
    1.8.0_201 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
```


If you run SQL Developer now it should work. I'm not sure about the side effects of removing this Java applet plugin. If I find it to be critical to other things I'll be sure to update this post.