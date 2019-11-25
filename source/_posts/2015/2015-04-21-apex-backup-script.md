---
title: APEX Backup Script
tags:
  - apex
  - open-source
date: 2015-04-21 11:35:00
alias:
---

A long time ago (almost 3 years ago to be exact), I blogged about [command line backups for APEX](http://www.talkapex.com/2012/04/command-line-backups-for-apex.html). The script was originally for DOS and required configurations to be stored directly in the script.

Since making the script available I've had numerous requests to update it to support Linux and Mac users. I've done a major overhaul of the backup script which is now available on Github [here](https://github.com/OraOpenSource/apexbackup).

The main changes are that all configurations can be stored in `custom.properties` and you won't lose your changes when you [update the scripts](https://github.com/OraOpenSource/apexbackup#updates) from the repository.

If you have any comments, issues, or feedback please submit them in the [project's issues page](https://github.com/OraOpenSource/apexbackup/issues).
