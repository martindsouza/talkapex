---
title: Oracle XE 18 Docker Image
tags:
  - docker
  - oracle
  - oracle-18c
date: 2018-10-27 09:31:04
alias:
---


Last week [Gerald Venzl](https://twitter.com/GeraldVenzl) announced on his [blog](https://geraldonit.com/2018/10/20/oracle-database-18c-express-edition-is-generally-available/) that Oracle XE 18c was available. You can download Oracle XE at [oracle.com/xe](http://oracle.com/xe).

Since it was announced [Adrian Png](https://twitter.com/fuzziebrain) created a Docker image and I've been working with him help ensure that the data files can be preserved even when the container is destroyed.

{% asset_img appdev_database-xe-alt_detailed.png %}

Using the new [Oracle XE 18c Docker](https://github.com/fuzziebrain/docker-oracle-xe) script you can now build your own Docker image and run your Oracle XE as a container. I recommend that anyone planning to use Oracle XE on their laptop look at using this Docker image rather installing locally or a VM (more on this in a future article). 

In the future Oracle may release its own official Docker image for Oracle XE. I will update this post when such an image is available.