---
title: How to Setup Oracle DB 12.2 Docker Container
tags:
  - docker
date: 2017-10-10 23:39:44
alias:
---


I recently had a need to do a Proof of Concept (POC) on a new feature released in Oracle 12c R2 (12.2). My first instinct was to go to my DBAs and ask them to give me access to a 12.2 environment. Thankfully I was at the [Oracle ACE](http://www.oracle.com/technetwork/community/oracle-ace/index.html) annual Oracle Open World dinner and [Gerald Venzl](https://twitter.com/geraldvenzl?lang=en) (aka Mr.Oracle-Docker) was there and convinced me otherwise. I must thank Gerald for both pushing me to start using Docker again and also for some support when I ran into issues.

The following post will cover how to get a 12.2 instance up and running using the Oracle Container Registry for your own personal instances

# Background

- If you haven't already done so, go install [Docker](https://www.docker.com). If you've never heard of Docker before I suggest reading a bit about it so you have some understanding of what is and how it works.
- Go to [container-registry.oracle.com](https://container-registry.oracle.com) and use your Oracle Technology Network (OTN) login and register.
  - One logged in go to `Database > Enterprise` and read and if you agree to the Terms and Conditions click the `Accept` button

# Docker

The following steps will pull the appropriate image(s) and setup your docker instance. I've added inline comments to describe each step. _If you're new to Docker please read my comments rather than blindly running the code._

```bash
# Some Docker versions may complain about login credentials when calling the pull command
# If you aren't auto prompted for login credentials run the following and use your OTN credentials
# docker login container-registry.oracle.com

# They're various docker 12c images. To help reduce the number (and size) of images on my laptop I only needed the 12.2 version
# This will take a while to run as the image size is around 3.5 GB
docker pull container-registry.oracle.com/database/enterprise:12.2.0.1

# For all the examples below the name "OracleDB" was used.
# You can use any name you want or just use the docker container ID to reference it

# To run the image the documentation says to just use the -P option for something like:
# docker run -d -it --name OracleDB -P container-registry.oracle.com/database/enterprise:12.2.0.1
# This will auto map a local port on your laptop to the container's Oracle 1521 port.
# The problem with this approach is that each time you stop and start the container you may get a new local port
# To get around this restriction you can use the the "-p" option for a statically defined port.
# In this example I chose 32711
docker run -d -it --name OracleDB -p 32711:1521 container-registry.oracle.com/database/enterprise:12.2.0.1

# It takes some time for the Oracle container to fully boot up. Before trying to connect to it check the container status by running:
docker ps

# Look in the STATUS column for the container. During "boot" time it will say "... (health: starting)".
# Wait until it says (healthy) before trying anything else.

# To review/confirm the port mapping run:
docker port OracleDB
# Should result in something like: 1521/tcp -> 0.0.0.0:32711

```

Your docker container should now be running. The following code shows how to connect to the instance using [SQLcl](http://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html) along with creating a test account on the PDB. You can take the connection strings in the SQLcl demos and apply to [SQL Developer](http://www.oracle.com/technetwork/developer-tools/sql-developer/overview/index.html).

```bash
# Note on my laptop I renamed "sql" to "sqlcl". Adjust the scripts accordingly or call sqlplus
# The difference between : and / at the end of the connection strings is :SID /SERVICE_NAME

# Connect to the CDB to create a new PDB user
# Note: Oradoc_db1 is the default password for the image
sqlcl sys/Oradoc_db1@0.0.0.0:32711:ORCLCDB as sysdba
```

In `SQL> `:

```sql
alter session set container = orclpdb1;

-- Create account to develop with
define new_user = 'martin'

create user &new_user. identified by &new_user. container = current;
grant connect, create view, create job, create table, create synonym, create sequence, create trigger, create procedure, create any context, create type to &new_user.;
grant unlimited tablespace to &new_user.;

exit
```

Connect to the PDB

```bash
sqlcl martin/martin@0.0.0.0:32711/ORCLPDB1.localdomain
```

Note: In SQL Developer this connection looks like:

{% asset_img docker-sql-developer.png %}

In `SQL> `

```sql
-- Create the emp and dept tables (note: only works in SQLcl / SQL Dev. Not SQL*Plus)
@https://raw.githubusercontent.com/OraOpenSource/OXAR/master/oracle/emp_dept.sql
commit;
exit;
```

You can now do/test whatever you want to do in your PDB!


# Common docker commands

So now that you've started your Docker instance, and established a working connection to the PDB how do you manage the Docker container? Here are some useful Docker commands:

```bash
# See all containers (running or otherwise)
docker ps -a

# Stop the docker image
docker stop OracleDB

# Start the docker image
docker start OracleDB

# *** Cleanup ***

# Delete container
docker rm OracleDB

# Delete Image
docker rmi container-registry.oracle.com/database/enterprise:12.2.0.1
```

# Final Thoughts

I've just started to use the Docker 12.2 image and may launch a 12.1 container as well (will blog instructions if I do it).

If I run into any issues doing my tests and "learning" development (i.e. kicking the 12.2 tires) I'll write another article and link below. I also plan to look into upgrading APEX on the 12.2 container along with creating a simple web server container to test APEX with some 12.2 features. If I get this working I'll blog about it.

**Update: ** [Role Hartman](https://twitter.com/RoelH) wrote a followup [post](http://roelhartman.blogspot.ca/2017/10/dockerize-your-apex-development.html) to this article that I highly suggest reading. I will update this article with some of his suggestions on how he sets up his Docker Oracle DB container.
