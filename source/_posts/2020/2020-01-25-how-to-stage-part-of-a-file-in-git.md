---
title: How to Stage Part of a File in Git
tags:
  - git
date: 2020-01-25 13:53:25
alias:
---


When working with [Git](https://git-scm.com/) (or any version control system) most developers tend to commit all the changes in a given file. What if you wanted to only commit part of the file? This situation can occur in a few situations, such as:

- Code not ready to be committed
- Working on two tickets at once and want separate commits for each set of work
- Some `TODO` comments that you don't want in the codebase

Git has a feature called [interactive staging](https://git-scm.com/book/en/v2/Git-Tools-Interactive-Staging) which lets you select specific lines of code you want staged (and then committed). The demo below highlights this using [Visual Studio Code](https://code.visualstudio.com/)'s (VSCode) Git tools.

In my current file I have the following:

{% asset_img demo-1.png %}

We can divide up the "new code" into two sections. The first `select` statement should be committed but the second statement (staring with `-- TODO mdsouza`) should not be committed. 

If the file needs to be committed without interactive staging a few options exist:

- Commit everything
- Cut out the sections I don't want in and paste them back in after commit
- Copy file (as a backup), remove sections I don't want, commit, restore backup

Neither of these options will lead to good results and take additional time. Using VSCode you can do interactive staging in multiple ways. The following shows how to stage specific lines.

{% asset_img git-demo.gif %}

A few things to note from the demo:

- Once staged, `test.sql` was still marked as having both changes and staged changes
- Only selected lines were staged (then committed)

*Note: In [Source Tree](https://www.sourcetreeapp.com/) (a free Git UI from [Atlassian](https://www.atlassian.com/)) this feature is called `hunk`s.*

Since finding out about interactive staging I've been using it to restrict what goes into each commit. It's allowed me to keep my commits more concise along with not having to commit work that isn't ready for others (ex: personal `TODO` comments).