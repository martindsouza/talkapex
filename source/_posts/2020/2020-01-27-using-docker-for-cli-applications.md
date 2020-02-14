---
title: Using Docker for CLI Applications
tags:
  - Docker
date: 2020-01-27 05:12:53
alias:
---



A while ago I wrote an article called {% post_link docker-as-a-micro-service %} which covered how I used [Docker](https://www.docker.com/) to run the Alexa CLI tool. This was my first time using Docker for a CLI application.

Since then, I've started to Dockerize other CLI tools and have found it very useful. Before I continue, I'll admit there's been some additional overhead both running a container for each command and also getting the Docker images correct. It has solved a few issues for me:

- I no longer worry about OS upgrade/changes on my laptop as my CLI commands all "reside" in the Docker image
- I avoid cluttering my system with a bunch of tools that can be a pain to remove if I don't like them
- Avoid all the installation, dependency issues, etc
- When I change laptops I don't need to reinstall all my tools. Docker will fetch them automatically the first time I call them.
  - This is also true for working with multiple machines


Going down the path of Dockerizing my CLI applications I've learned a few things that others may find  helpful looking to take the same approach:

- Use `` `pwd` `` to evaluate your current directory as a mount volume to the container (*demo in next point*)
- Use single quotes around the `alias` definition so that expressions aren't evaluated until run time. Ex:

```bash
# Double Quote issue
alias dummycommand="docker run -it --rm \
  -v `pwd`:/opt/node_app \
  somedockerimage:latest"

# This will always (no matter where I call dummycommand from) 
# map "~/" on my laptop to "/opt/node_app" on the container

# Single Quote (Solution)
alias dummycommand='docker run -it --rm \
  -v `pwd`:/opt/node_app/app \
  somedockerimage:latest'

# This will always map the current directory I'm in on my 
# laptop to "/opt/node_app/app" on the container
```

In the next few articles I'll write about some of the CLI tools I use and how I've moved them to Docker.

- {% post_link docker-image-for-oracle-sqlcl %}
- {% post_link docker-for-oracle-sql-plus %}