---
title: Logger 2.1.0 - Documentation Overhaul
tags:
  - logger
date: 2013-08-06 07:00:00
alias:
---

_In preparation for the release of Logger 2.1.0 I've decided to write a few posts highlighting some of the new features._

As [Logger](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility) expands, so does its documentation. We have reached a point that it was  no longer feasible to continue with a one page documentation structure.  Besides being hard to find a specific bit of information I also found  that it was hard to get a quick overview of all the functions that  Logger has.

The documentation for Logger has been  completely revamped. It's broken up into two main sections: the initial readme file (found on the main [project page](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility)) and a [wiki](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki).  The readme file contains a brief overview of logger and links to the  wiki. The wiki has been broken up into four sub sections: [Installation](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Installation), [Logger API](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Logger-API), [Change Log](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Change%20Logs), and [Best Practices](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Best-Practices).  This is a start to make Logger's documentation similar to Oracle's  documentation format which should make things consistent for developers.

If you've already used Logger for a while, take a look at the [Logger API](https://github.com/tmuth/Logger---A-PL-SQL-Logging-Utility/wiki/Logger-API) page. I think you'll find some functions that you didn't know existed.

The  wiki documents are stored in a separate repository on GitHub. This  means that they aren't synced with a release is tagged. To get around  this issue, each build now contains a folder called "wiki". In it, you  will find all the wiki files (in markdown format) generated at the time  of the build.

On a side note, the documentation  restructuring took a lot of time. I didn't have enough time to document  all the functions and they will all be updated in the near future once I  have more time. Of course you're encouraged to fork the project and  update the documentation if you want to help out.
