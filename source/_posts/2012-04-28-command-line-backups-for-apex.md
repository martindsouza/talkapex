---
title: Command Line Backups for APEX Applications
tags:
  - apex
date: 2012-04-28 17:25:00
alias:
---

<span style="color: red;">**Update 21-Apr-2015: **This script will no longer be supported. Instead please use the new script (for Mac/Linux) available on [Github](https://github.com/OraOpenSource/apexbackup). New article:&nbsp;[http://www.talkapex.com/2015/04/apex-backup-script.html](http://www.talkapex.com/2015/04/apex-backup-script.html)</span>

One thing developers like to do is keep local backup copies of their development work. Sometimes they'll copy files and suffix them with _.bak_ or they'll store them in a version control repository. With APEX you don't copy a file to back it up. Either you have to copy the application (within the workspace) or export the application and save it locally. For one-off backups this can be ok, but as you develop with more and more applications in larger environments it can be tedious work and will slow down your development time.

Thankfully APEX has a tool, that comes as part of the install zip file, which allows for command line exports. It's one of the lesser known tools that is extremely useful.

Below is a simplified copy of a batch file which will automatically backup all the APEX applications on a given database using the APEX backup tool. I usually recommend that organizations run such a script on their development environments to store hourly backups of their development environments. The example is a very simple example which should be modified to tag the backup with the date/time or zip and store the backups in version control. I've also appended an extra script which lists all the APEX applications, workspaces, and IDs and stores this information as part of the backup in a _.html_ file.

To use the script, create the files listed below in the same directory and make the appropriate modifications in _apex_backup.bat_

**apex_backup.bat**
_Backups all the applications in the database_
<pre class="brush: bash;">REM ***** START MODIFICATIONS ******

REM ***** Parameters ******
REM Oracle information
REM To find SID name: select * from v$instance;
set oracleSID=XE
set oracleSystemPass=oracle
set oraclePort=1526
set oracleHost=localhost
REM Root directory where oracle\apex\*class files are stored
REM These is a sub directory from the extracted APEX install zip file
set apexExportJavaDir=C:\temp\apex_4.1.1_en\apex\utilities
REM File that contains names of applications and workspaces
set apexHtmlFileName=apex_info.html
REM Directory where backups will be stored
set backupLocation=C:\temp\apexbackup

REM ****** PATHS *********
REM Note: You may not need to explicitly define these as they may already be set in OS.
set ORACLE_HOME=C:\oracle\product\11.2.0\client_1
set CLASSPATH=%CLASSPATH%;.\;%ORACLE_HOME%\jdbc\lib\ojdbc5.jar;%apexExportJavaDir%
set PATH=%PATH%;.\;C:\Program Files (x86)\Java\jre6\bin

REM ***** END MODIFICATIONS ******

REM ****** Other *********
set startRunLocation=%CD%

REM ****** Directory Setup ******
REM Create temp bacpkup location
mkdir %backupLocation%

REM ****** APEX BACKUP *******
REM Go to backup location to run backups in
cd %backupLocation%

REM Export all applications
java oracle.apex.APEXExport -db %oracleHost%:%oraclePort%:%oracleSID% -user system -password %oracleSystemPass% -instance

REM Export all Workspaces
java oracle.apex.APEXExport -db %oracleHost%:%oraclePort%:%oracleSID% -user system -password %oracleSystemPass% -expWorkspace

REM Generate listing of Workspaces and Applications
sqlplus system/%oracleSystemPass%@%oracleHost%:%oraclePort%/%oracleSID% @%startRunLocation%\apex_backup_info.sql %apexHtmlFileName%

REM Back to start location
cd %startRunLocation%
</pre>**apex_backup_info.sql**
_Stores all the application names, workspaces, and IDs_
<pre class="brush: sql;">-- Parameters
-- 1 = File Name

-- http://forums.devshed.com/oracle-development-96/sql-plus-column-width-format-185085.html
-- http://download.oracle.com/docs/cd/B10501_01/server.920/a90842/ch13.htm#1012748
SET NUMFORMAT 9999999999999999999999999999999999999
SET MARKUP HTML ON SPOOL ON HEAD "<title>APEX Export</title>"

SET ECHO OFF
SPOOL &amp;1

-- Current Date
select to_char(sysdate, 'DD-MON-YYYY HH24:MI:SS') current_date
from dual;

-- Applications
SELECT application_id, workspace, application_name, owner
FROM apex_applications
WHERE workspace != 'INTERNAL'
ORDER BY application_id;

-- Workspaces
SELECT workspace_id, workspace
FROM apex_workspaces
WHERE workspace != 'INTERNAL'
ORDER BY workspace_id;

SPOOL OFF
SET MARKUP HTML OFF
SET ECHO ON  

EXIT;
</pre>
