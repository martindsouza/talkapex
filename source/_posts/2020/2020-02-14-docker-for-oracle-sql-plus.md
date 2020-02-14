---
title: Docker for Oracle SQL*Plus
tags:
  - docker
  - sqlplus
date: 2020-02-14 18:19:32
alias:
---


*This article is part of a series on using Docker for CLI tools. You can read the original article {% post_link using-docker-for-cli-applications here %}.*

Oracle SQL*Plus is the Command Line (CLI) tool to connect to Oracle. It is part of the [Oracle Instant Client](https://www.oracle.com/ca-en/database/technologies/instant-client.html) set of tools. I've always found the installation process to be cumbersome as it requires a few steps (not a straight install or base executable like [SQLcl](https://www.oracle.com/ca-en/database/technologies/appdev/sqlcl.html)).

Oracle has made the Instant Client files now available on a pre-built Docker image. This article covers how to download the Docker image and use it to run SQl*Plus in a container rather than a local install.

## Accept License Agreements

Oracle has its own [container registry](https://container-registry.oracle.com). Before using any of their pre-built images you must accept the license agreements (this only has to be done once).

- Go to [container registry](https://container-registry.oracle.com)
- Search for `instantclient` (all one word)
  - Select the `instantclient` link
- Sign In (image below) and accept the license agreements

{% asset_img oracle-container-registry.png %}


## Docker Login to Oracle's Container Registry

In your terminal run: 

```bash
docker login container-registry.oracle.com
```

You will be required to enter in your Oracle SSO login credentials (only need to do this once).


## Setup `sqlplus` alias

In `~/.bash_profile` or `~/.zshrc` add the following alias

```bash
alias sqlplus='docker run -it --rm \
  -e ORACLE_PATH=/oracle/ \
  -e TNS_ADMIN=\$TNS_ADMIN \
  -w /myhost \
  --network="host" \
  -v ~/Documents/Oracle/:/oracle/ \
  -v `pwd`:/myhost \
  container-registry.oracle.com/database/instantclient:latest \
  sqlplus '
```

## Run SQL*Plus

Start a new terminal (so the alias is registered) and just run `sqlplus` as you normally would. 


## Considerations

The current image from Oracle is using Instant Client `12.2.0.1.0` where as at the time of writing the most current version is `19.3.0.0.0`. If you want to build your own image so you can run the latest version, you can use Oracle's [Docker Image files](https://github.com/oracle/docker-images/tree/master/OracleInstantClient) (just search for the version you want). 






