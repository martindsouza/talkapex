---
title: Docker Image for ORDS Standalone
tags:
  - docker
  - ords
date: 2018-04-01 20:00:42
alias:
---


A while ago I released a Docker image for [ORDS using Tocmat](https://github.com/martindsouza/docker-ords/releases/tag/tomcat-0.1.0). I've tagged that script and have since rebuilt this image to use ORDS standalone as well as updating it to `17.4.1`.

If you're interested in using it, the entire project along with documentation is available here: [github.com/martindsouza/docker-ords](https://github.com/martindsouza/docker-ords). 

A few things to note about this Docker image:

- The configuration folder points to a mountable volume. If you store your configuration on a shared folder or version control system you can have ORDS point at it. If you need to have multiple versions of ORDS running this can be helpful since they can all point to the same configuration.
  - If you don't have a shared configuration that's fine. The container will install and configure ORDS if one isn't found.
- I could not post this image on Docker Hub as Oracle requires that you [download ORDS](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html) yourself and accept their terms and conditions. This means that you need to build your own Docker image (this adds a few minimal steps to the overall process).
- The image currently does not have SSL support and can be added it in if there's interest. Please vote or leave a comment on the ["Add Support for SSL"](https://github.com/martindsouza/docker-ords/issues/7) issue if you'd like it.

If you have any other feedback or suggestions please create an [issue](https://github.com/martindsouza/docker-ords/issues) on the project page.

