---
title: Installing SQLcl
tags:
  - sqlcl
date: 2015-04-13 07:00:00
alias:
---

SQLcl is the new command line tool from Oracle, more specifically from the [SQL Developer team](https://twitter.com/oraclesqldev). It is currently an Early Adopter (EA) release and you can download it from: [http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/sqldev-41ea-2372780.html](http://www.oracle.com/technetwork/developer-tools/sql-developer/downloads/sqldev-41ea-2372780.html)

After testing it for a while I was hooked and plan on using it as a full time replacement for SQL*Plus (which I think is the intent of the product).

The only difficulty I had was where to store it on OS X so it was accessible everywhere. Here's how I "installed" it and hopefully this will be useful for others:

```bash
cd ~/Downloads
#Note: version/filename of file will change for each release
unzip sqlcl-4.1.0.15.067.0446-no-jre.zip

#Assuming you already have an /oracle directory (I had one for the instant client)
cp -r sqlcl /oracle

#Give sqlcl execute permission
cd /oracle/sqlcl/bin
chmod 755 sql

#rename to sqlcl so no confusion (optional)
mv sql sqlcl

#Add directory to path so can run anywhere in the command line:

#Temporary access:
PATH=$PATH:/oracle/sqlcl/bin

#Permanent access:
#Note: See Barry's comment below about storing in /etc/paths.d on Mac
vi ~/.bash_profile
#Add just above the export PATH line
PATH=$PATH:/oracle/sqlcl/bin
```

In the above example I installed SQLcl in the `/oracle` directory. You could also put it anywhere you want such as `/usr/local/oracle` etc. Just make sure that you reference the location in the `PATH` environment variable.

If you're looking for more info on SQLcl as well as some excellent examples check out Kris Rice's [blog](http://krisrice.blogspot.com/) and Jeff Smith's [blog](http://www.thatjeffsmith.com/).

_Update: The following article covers how to [create login scripts for SQLcl](http://www.talkapex.com/2015/05/sqlcl-and-loginsql.html)._
