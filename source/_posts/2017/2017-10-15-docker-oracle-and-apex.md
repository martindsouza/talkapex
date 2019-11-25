---
title: Docker Oracle and APEX
tags:
  - docker
  - apex
  - oracle
date: 2017-10-15 19:12:39
alias:
---
_Update: I've moved my setup process to Dockerize Oracle and APEX to Github. It will be maintained with the latest steps I'm using it. The repository is [docker-oracle-setup](https://github.com/martindsouza/docker-oracle-setup)._

Last week I needed to test an Oracle 12.2 feature and got hooked on Docker. I wrote an article on [how to setup an Oracle DB 12.2 Docker container](http://www.talkapex.com/2017/10/how-to-setup-oracle-db-12-2-docker-container/). This solved my goal to test the 12.2 feature. [Roel Hartman](https://twitter.com/RoelH) then wrote a [followup article](http://roelhartman.blogspot.ca/2017/10/dockerize-your-apex-development.html) about how to setup an Oracle database in a Docker container but preserve the data on his laptop. I.e. if the container was deleted or needed to be rebuilt his database data wouldn't be lost.

Roel's article inspired me to setup an entire Dockerized Oracle and APEX environment, thus replacing my current local VM infrastructure. I took a lot of notes about the process. The result is this blog post which contains everything I did to create my containers and link them together. To get it all working I used the following articles and all my scripts are a result of a combination of the code found in the links.

- [APEX and ORDS up and running in....2 steps!](http://joelkallman.blogspot.ca/2017/05/apex-and-ords-up-and-running-in2-steps.html) by [Joel Kallman](https://twitter.com/joelkallman)
- [Dockerize your APEX development environment](http://roelhartman.blogspot.ca/2017/10/dockerize-your-apex-development.html) by [Roel Hartman](https://twitter.com/RoelH)
- [Oracle Database 12c now available on Docker](https://sqlmaria.com/2017/04/27/oracle-database-12c-now-available-on-docker/) by [Maria Colgan](https://twitter.com/sqlmaria)
- [ORDS Docker Setup](https://github.com/lucassampsouza/ords_apex) by [Lucas Souza](http://www.vertech-it.com.br)


## Background

A few things to keep in mind that will help when reading the rest of this article:

- All my scripts are Linux / MacOS focused. If you use a Windows machine you'll need to translate
- I specifically made reference to "your laptop" to emphasize what was run "on your machine" vs "in a docker container"

The following key configurations were used for the containers:


### Port Mapping
Container | Port | Description
--- | --- | ---
`oracle` | `32712` | TNS listener
`ords` | `32713` | ORDS

### Passwords
Container | Username | Password | Description
--- | --- | --- | ---
`oracle`  | `sys` |  `Oradoc_db1` |
`oracle`  | `admin` |  `oracle` | Workspace `Internal` for APEX admin

### Download Files
Due to licensing restrictions I can't host/provide these files in Github or elsewhere. As such you'll need to download them manually. Download the following files and store them in your `Downloads` folder

- Docker Images: [github.com/oracle/docker-images](https://github.com/oracle/docker-images). This will be saved as `docker-images-master.zip`
- Oracle 12.2 Database: Go to [www.oracle.com/technetwork/database/enterprise-edition/downloads](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) and download `linuxx64_12201_database.zip`
- APEX: Go to [www.oracle.com/technetwork/developer-tools/apex/downloads](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) and download latest version of APEX. For this blog it was `5.1.3`
- ORDS: Go to [www.oracle.com/technetwork/developer-tools/rest-data-services/downloads](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html) and download latest version of ORDS. For this blog it was `3.0.12`

## Laptop Folder Structure
The following script will create a folder structure that looks like:

Path | Description
--- | ---
`~/docker` | root
`~/docker/apex` | To host APEX installation and images for each version
`~/docker/apex/5.1.3` | APEX 5.1.3 installation files  
`~/docker/oracle`  |  Hold Oracle 12.2 data files
`~/docker/ords`  |  ORDS Dockerfile (to build ORDS image)
`~/docker/tmp`  |  Temp folder

```bash
mkdir ~/docker

# The APEX folder structure allows for multiple versions of APEX to be hosted by different ORDS instances
mkdir ~/docker/apex
mkdir ~/docker/apex/5.1.3

# To store oracle data
mkdir ~/docker/oracle
#Oracle REST Data Services
mkdir ~/docker/ords
```

## Move Downloaded Files
```bash
# ORDS (done in ORDS section)

# APEX
cd ~/docker/apex/
mv ~/Downloads/apex_5.1.3.zip .
unzip apex_5.1.3.zip
mv apex 5.1.3

# Oracle Docker files
mkdir ~/docker/tmp
cd ~/docker/tmp
mv ~/Downloads/docker-images-master.zip .
mv ~/Downloads/linuxx64_12201_database.zip .
```


## Docker

### Build Oracle Docker Image

This will be a different setup then I [previously blogged about](http://www.talkapex.com/2017/10/how-to-setup-oracle-db-12-2-docker-container/) in that this docker container will separate the data from the app. I.e. we can remove the container and rebuild it while keeping all the data in place. This allows for us to

```bash
cd ~/docker/tmp
unzip docker-images-master.zip
mv linuxx64_12201_database.zip docker-images-master/OracleDatabase/dockerfiles/12.2.0.1
cd docker-images-master/OracleDatabase/dockerfiles
./buildDockerImage.sh -v 12.2.0.1 -e

# Once completed should see a message like:
# Successfully built ad2c10d804a7
# Successfully tagged oracle/database:12.2.0.1-ee
#
#   Oracle Database Docker Image for 'ee' version 12.2.0.1 is ready to be extended:
#
#     --> oracle/database:12.2.0.1-ee
#
#   Build completed in 678 seconds.
```

### Setup Docker Network

In order for the containers to "talk" to each other we need to setup a Docker network and associate all the containers on this network. Containers can reference each other by their respective container names. When referencing another container on the same Docker network the port used is the container's native port **not** the mapped port on your laptop.

```bash
docker network create oracle_network

# Other docker network commands (don't need to run them as part of install)

# Connect and existing container to a docker network
# docker network connect <network name> <container name>

# View a network and connected containers
# In this example "oracle_network" is the network we're interested in
docker network inspect oracle_network
```

### Create Oracle Docker Container

The following command will create and run the Oracle Docker container. It's TNS listener will be accesible via port `32712` on your laptop. The reference to the APEX installation files are necessary only whe installing APEX.

Adding the `-e TZ` will set the appropriate timezine for the OS and the database. A full list of timezones can be found [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). If excluded it will default to UTC.

```bash
docker run \
  --name oracle \
  --network=oracle_network \
  -e TZ=America/Edmonton \
  -p 32712:1521 \
  -v ~/docker/oracle:/opt/oracle/oradata \
  -v ~/docker/apex/5.1.3:/tmp/apex-install \
  oracle/database:12.2.0.1-ee

# Once the Docker image is working you'll see something like:
# #########################
# DATABASE IS READY TO USE!
# #########################
# It will then have a bunch of other information showing which you can ignore.
```

In another terminal tab, set the `sys` password to `Oradoc_db1` and install APEX

```bash
docker exec oracle ./setPassword.sh Oradoc_db1
```

### Install APEX
In a terminal tab on your laptop run:

```bash
# Install and configure APEX
docker exec -it oracle bash -c "source /home/oracle/.bashrc; bash"

cd /tmp/apex-install
sqlplus sys/Oradoc_db1@localhost/orclpdb1 as sysdba
```

The above command will open a `SQL` prompt. Run the following scripts:

```sql
-- Install APEX
@apexins.sql SYSAUX SYSAUX TEMP /i/

-- APEX REST configuration
@apex_rest_config_core.sql oracle oracle

-- Required for ORDS install
alter user apex_public_user identified by oracle account unlock;

-- From Joel's blog: "Create a network ACE for APEX (this is used when consuming Web services or sending outbound mail):"
declare
    l_acl_path varchar2(4000);
    l_apex_schema varchar2(100);
begin
    for c1 in (select schema
                 from sys.dba_registry
                where comp_id = 'APEX') loop
        l_apex_schema := c1.schema;
    end loop;
    sys.dbms_network_acl_admin.append_host_ace(
        host => '*',
        ace => xs$ace_type(privilege_list => xs$name_list('connect'),
        principal_name => l_apex_schema,
        principal_type => xs_acl.ptype_db));
    commit;
end;
/

-- Setup APEX Admin password
begin
    apex_util.set_security_group_id( 10 );
    apex_util.create_user(
        p_user_name => 'ADMIN',
        p_email_address => 'martin@talkapex.com',
        p_web_password => 'oracle',
        p_developer_privs => 'ADMIN' );
    apex_util.set_security_group_id( null );
    commit;
end;
/

-- Exit SQL
exit
```

Now exit bash:
```bash
# Exit bash
exit
```

### Create ORDS Container

The following assumes that you've downloaded ORDS `3.0.12`. Referencing the ORDS version so that can create ORDS images for each ORDS release.

The scripts below will first create the ORDS Docker image then create the containers.

```bash
# Uses https://github.com/martindsouza/docker-ords Dockerfile
cd ~/docker/ords
git clone git@github.com:martindsouza/docker-ords.git .
mv ~/Downloads/ords.3.0.12.263.15.32.zip ~/docker/ords
# Only need ords.war from install
unzip ~/docker/ords/ords.3.0.12.263.15.32.zip ords.war

# Build the Docker Image
docker build -t ords:3.0.12 .

# Run ORDS
# Note: Leave DB_PORT=1521 as this is NOT the port that maps to your laptop,
# rather the internal docker network that was created. I.e. container to container
docker run -t -i \
  --name ords \
  --network=oracle_network \
  -e DB_HOSTNAME=oracle \
  -e DB_PORT=1521 \
  -e DB_SERVICENAME=ORCLPDB1 \
  -e APEX_PUBLIC_USER_PASS=oracle \
  -e APEX_LISTENER_PASS=oracle \
  -e APEX_REST_PASS=oracle \
  -e ORDS_PASS=oracle \
  -e SYS_PASS=Oradoc_db1 \
  --volume /Users/giffy/docker/apex/5.1.3/images:/usr/local/tomcat/webapps/i \
  -p 32713:8080 \
  ords:3.0.12
```

You should now be able to go to [localhost:32713/ords/](http://localhost:32713/ords/) on your laptop to run APEX

## Useful Commands

### Docker Start/Stop Containers
```bash
docker stop -t 200 oracle
docker stop -t 200 ords

# Start containers
docker start oracle

# Wait a minute to start ORDS as Oracle will take a few minutes to boot
docker start ords
```

### Connect to DB from laptop

`sqlcl` is my alias for [SQLcl](http://www.oracle.com/technetwork/developer-tools/sqlcl/overview/index.html). More information on how to install SQLcl on MacOS [here](http://www.talkapex.com/2015/04/installing-sqlcl/).
```bash
# CDB (not usually required)
sqlcl sys/Oradoc_db1@localhost:32712:orclcdb as sysdba
# PDB
sqlcl sys/Oradoc_db1@localhost:32712/orclpdb1 as sysdba
sqlcl martin/martin@localhost:32712/orclpdb1

```

### Create New DB User
```sql
define new_user = 'martin'
create user &new_user. identified by &new_user. container = current;
grant connect, resource, create any context to &new_user;
alter user &new_user quota unlimited on users;
```
