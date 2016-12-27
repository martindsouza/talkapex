---
title: Oracle Instant Client on Mac OS X
tags:
  - macos
  - oracle
date: 2013-03-29 18:37:00
alias:
---

A while back I broke down to the peer pressure in the APEX community (you know who you are ;-) and bought a Mac Book Air. I'm still learning the ropes and one thing that took some time to get working was the Oracle Instant Client. Installing it isn't as straightforward as installing the Instant Client on Windows.

I finally got it working and thought I'd post what I did for others that are new to OS X.

**Download Instant Client**

First you'll nee to download the [Instant Client for OS X](http://www.oracle.com/technetwork/topics/intel-macsoft-096467.html). I downloaded the following files for Version 11.2.0.3.0 (64-bit):

- instantclient-basic-macos.x64-11.2.0.3.0.zip
- instantclient-sqlplus-macos.x64-11.2.0.3.0.zip

Unzip both files and put their contents in <span style="font-family: &quot;Courier New&quot;,Courier,monospace;">/oracle/instantclient_11_2/</span>

**Setting Paths**

You'll need to set the appropriate paths to load by default in Terminal**. **Open Terminal** **and run:
<pre class="brush: bash;">vi ~/.bash_profile
</pre>Add the following to the file:
<pre class="brush: bash;">#in ~/.bash_profile
DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:/oracle/instantclient_11_2
export DYLD_LIBRARY_PATH

PATH=$PATH:/oracle/instantclient_11_2/
export PATH

#Create this directory and put your tnsnames.ora file in it
TNS_ADMIN=/oracle/instantclient_11_2/network/admin/
</pre>**Running SQL*Plus**

Now when you open a new terminal window you should be able to run SQL*Plus
<pre class="brush: bash;">#ex: sqlplus username/password@//server:port/sid
sqlplus system/oracle@//localhost:1521/XE
#or use a connection string leveraging a tnsnames entry
</pre>
**Additional Addons**

When using SQL*Plus in Windows you can use the Up arrow to get your previous command. If you do that in Linux you'll** **get some weird character. The good news is that there's a program to resolve it called rlwrap. CJ Travis has a good post on how to [install rlwrap of Mac OS X](http://www.cjtravis.com/?p=744).
