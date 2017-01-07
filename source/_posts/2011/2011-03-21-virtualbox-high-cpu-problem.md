---
title: VirtualBox - High CPU Problem
tags:
  - oracle
date: 2011-03-21 08:00:00
alias:
---

I've been using Oracle's [VirtualBox](http://www.virtualbox.org/) for several months now. I'm really impressed with this product and it has helped me a lot to rapidly prototype some APEX concepts.

One thing that bothered me when I started using it was that no matter what my virtual machine was doing, [VirtualBox](http://www.virtualbox.org/) was using a lot of the CPU on my laptop. This didn't matter if the virtual machine was idle or doing any processing. After some digging around I found this very helpful post on the Oracle forums: [http://forums.oracle.com/forums/thread.jspa?messageID=9305831](http://forums.oracle.com/forums/thread.jspa?messageID=9305831#9305831). As weird as it may sound the suggestion is to create and start a second, dummy, virtual machine. I was pleasantly surprised to see that it worked. The configuration that I used was: <span style="font-style:italic;">DOS as an OS. 4 MB of memory and a 4 MB hard drive. Disable all the extra stuff (network, audio, usb, etc)</span>.

Here are the screen shots of my CPU before and after I started the second, dummy, virtual machine:

<span style="font-weight:bold;font-style:italic">Before:</span>
[![](http://4.bp.blogspot.com/-YEgldzE7Kks/TYRGDtmnfRI/AAAAAAAAD38/ceWRQ4ZSgZ0/s400/virtual_box_cpu_before.jpg)](http://4.bp.blogspot.com/-YEgldzE7Kks/TYRGDtmnfRI/AAAAAAAAD38/ceWRQ4ZSgZ0/s1600/virtual_box_cpu_before.jpg)
<span style="font-weight:bold;font-style:italic">After:</span>
[![](http://1.bp.blogspot.com/-TrSQwDmKRKw/TYRGDy-nUTI/AAAAAAAAD4E/9l-kydkrbEE/s400/virtual_box_cpu_after.jpg)](http://1.bp.blogspot.com/-TrSQwDmKRKw/TYRGDy-nUTI/AAAAAAAAD4E/9l-kydkrbEE/s1600/virtual_box_cpu_after.jpg)
After starting the second virtual machine the CPU spiked for a few seconds then dropped significantly. Hopefully this issue will be fixed in a future version of [VirtualBox](http://www.virtualbox.org/) but until then this is a very simple work around.
