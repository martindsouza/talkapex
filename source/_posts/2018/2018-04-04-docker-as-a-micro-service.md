---
title: Docker as a Micro Service
tags:
  - docker
date: 2018-04-04 22:41:37
alias:
---


One thing I really dislike when trying to install new command line (CLI) software is the uninstall process isn't always very clear. Things can get worse if the software has additional dependencies (Java for example) especially when you need to have two versions of the same tool for different apps.

Docker solves this problem by keeping everything you need for a specific task in one container. The added bonus is that you'll probably be able to find a Docker container for just about anything you want and it's OS independent on [Docker Hub](https://hub.docker.com/).

At [Insum's 2018 Hackathon](https://www.insum.ca/hackathon-opportunity-work-with-new-technology/) we developed skills for [Amazon Alexa](https://alexa.amazon.com). To help the teams I published a [Docker image](https://hub.docker.com/r/martindsouza/amazon-ask-cli/) that has both the [AWS CLI](https://aws.amazon.com/cli/) and [Alexa Skills Kit (ASK) CLI](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html). 

Most of the demos I've seen for containers that are used for CLI tools looked like this:

```bash
docker run -it \
  ...
  doccker_prefs
  my_tool.sh -some -parameters
```

Though these containers are helpful it's not really useful for end users (i.e. developers) since the command can be tough to remember and the containers may exist after running the tool (i.e. you may need to manually delete them.

When building the [Amazon ASK CLI container](https://hub.docker.com/r/martindsouza/amazon-ask-cli/) I wanted to avoid these issues to make it as easy as possible for developers to use. I did the following tricks:

## `alias` the `docker run` command

Instead of having to remember the long `docker run` command with all the parameters I used an `alias` command to bundle it all together. Mine looked like this:

```bash
alias alexa="docker run -it --rm \
  -v ~/alexa-demo/ask-config:/home/node/.ask \
  -v ~/alexa-demo/aws-config:/home/node/.aws \
  -v ~/alexa-demo/app:/home/node/app \
  martindsouza/amazon-ask-cli:latest 
```

A few key things to note from the above snippet:

- `alias`: This means that anytime I type in `alexa` in bash it will run everything in the quotes.
  - _Windows users: `alias` doesn't exist in DOS or PowerShell. I'm not aware of any alternatives. If you know of any easy ones please leave a comment and I'll update this post_
- `--rm` option: Deletes (self destruct) the container after the command is run. This means is that when I run `alexa ask init` (where `ask init` is the CLI tool) it'll `run` the container, execute `ask` (with parameter `init`), then remove the container once `ask` is done.

Using the `alias` and `--rm` techniques you can easily have all the CLI tools you want without cluttering up your base machine. Of course there may be some minor performance impacts by Dockerizing them. Personally I didn't notice any slowdowns and will start using this technique for all my future CLI tools.

