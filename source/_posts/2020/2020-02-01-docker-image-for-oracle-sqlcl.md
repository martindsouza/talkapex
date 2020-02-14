---
title: Docker Image for Oracle SQLcl
tags:
  - docker
  - sqlcl
date: 2020-02-01 05:11:00
alias:
---


*This article is part of a series on using Docker for CLI tools. You can read the original article {% post_link using-docker-for-cli-applications here %}.*

Oracle [SQLcl](https://www.oracle.com/ca-en/database/technologies/appdev/sqlcl.html) is the new command line (CLI) tool for connecting to the database. I prefer to use it instead of SQL*Plus as it has a lot of new features and is constantly upgraded.

SQLcl is fairly easy to install. Just download and unzip the file then you can run it. The caveat is that it requires Java Runtime Engine (JRE) 8. For most people the JRE dependency isn't an issue but for some it may cause problems. 

I built a Docker image for SQLcl and it is available on [Github](https://github.com/martindsouza/docker-oracle-sqlcl). The catch is that you need to clone the repo and build the image yourself (due to Oracle licensing restrictions). If this changes in the future I'll update the repo so a Docker image is available on [Docker Hub](https://hub.docker.com/).

The Github page covers how to clone and setup the `alias` to run SQLcl via a Docker container. A demo of it in action is included below. A few notes about the demo:

- The initial command `docker ps |grep sql` shows all the active docker containers
- SQLcl is run via a bash alias (*see [Github](https://github.com/martindsouza/docker-oracle-sqlcl) instructions for how to do this*).
  - It kicks off my local `login.sql` script that is on my laptop (not on the docker container).
- `docker ps |grep sql` is run again (in different terminal) to show that SQLcl is actually being run as a Docker container

{% asset_img docker-sqlcl.gif %}