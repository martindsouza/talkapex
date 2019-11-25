---
title: Automatically Update an Application's Version Number in APEX
tags:
  - apex
date: 2017-04-30 06:37:14
alias:
---


Last week [Connor McDonald](https://twitter.com/connor_mc_d) tweeted that he received the following question on [AskTom](https://asktom.oracle.com): "_I hacked the Apex install scripts to get access to the APEX... schema..and now Apex doesnt work_" The community had a few [reactions on twitter](https://twitter.com/connor_mc_d/status/857771927842078721) including my comment which stated that "_They’re some cases where it’s good. I use it to “inject” the version as part of the build process._"

I had mis-read Connor's statement and interpreted "_hacked the APEX install scripts_" as "_hacked an application install script_". To correct my statement, you should NEVER modify the main APEX installation script. That said, I usually modify an application's install script despite the fact that I shouldn't. I have a good reason though which is described below.

Each APEX application has a meta data property called `Version` and is usually displayed in the bottom of most applications by referencing the substitution string `APP_VERSION` in the page's template as shown below.

<img src="{% asset_path apex-version-number.png %}">

The application's version is statically defined in the application's properties which can be set in Shared Components > Application Definition: 

<img src="{% asset_path apex-version-property.png %}">

Since the properly must be statically defined it requires a manual step to update the version number each time the application is released. This usually requires someone to update this value before exporting the application for each release. Personally I'm not a fan of this as I try to automate as much as possible for each build and it can easily lead to mistakes.

To get around this I use a mnemonic string as the release version and replace it using a script as part of my build process. Here's an example.

Change the version number to `%RELEASE_VERSION%`:

<img src="{% asset_path release-version.png %}">

As part of your build script you should be exporting the APEX application from command line. [SQLcl](http://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html) has a built in command to easily do this that [Kris Rice](https://twitter.com/krisrice) blogged about [here](http://krisrice.blogspot.ca/2016/10/export-apex-application-with-sqlcl.html).

The export file will now look like:

<img src="{% asset_path apex-export.png %}">

You can now use a command like [`sed`](https://www.digitalocean.com/community/tutorials/the-basics-of-using-the-sed-stream-editor-to-manipulate-text-in-linux) in Linux or Node.js to find and replace `%RELEASE_VERSION%` with your build release version. An example of a build script that both exports the application and sets the version number:

`build.sh`:

```bash
VERSION=$1

echo exit | sqlcl giffy/giffy@localhost:1521/xe @apex_export.sql 123

sed -i "" "s/%RELEASE_VERSION%/$VERSION/" f123.sql
```

`apex_export.sql`:

```sql
set termout off

define APP_ID = &1

spool f&APP_ID..sql
apex export &APP_ID.
spool off
```

Running:

```bash
./build.sh 1.0.0
```








