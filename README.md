[![linuxserver.io](https://raw.githubusercontent.com/linuxserver/docker-templates/master/linuxserver.io/img/linuxserver_medium.png)](https://linuxserver.io)

The [LinuxServer.io](https://linuxserver.io) team brings you another container release featuring :-

 * regular and timely application updates
 * easy user mappings (PGID, PUID)
 * custom base image with s6 overlay
 * weekly base OS updates with common layers across the entire LinuxServer.io ecosystem to minimise space usage, down time and bandwidth
 * regular security updates

Find us at:
* [Discord](https://discord.gg/YWrKVTn) - realtime support / chat with the community and the team.
* [IRC](https://irc.linuxserver.io) - on freenode at `#linuxserver.io`. Our primary support channel is Discord.
* [Blog](https://blog.linuxserver.io) - all the things you can do with our containers including How-To guides, opinions and much more!
* [Podcast](https://anchor.fm/linuxserverio) - on hiatus. Coming back soon (late 2018).

# PSA: Changes are happening

From August 2018 onwards, Linuxserver are in the midst of switching to a new CI platform which will enable us to build and release multiple architectures under a single repo. To this end, existing images for `arm64` and `armhf` builds are being deprecated. They are replaced by a manifest file in each container which automatically pulls the correct image for your architecture. You'll also be able to pull based on a specific architecture tag.

TLDR: Multi-arch support is changing from multiple repos to one repo per container image.

# [linuxserver/clarkson](https://github.com/linuxserver/docker-clarkson)
[![](https://img.shields.io/discord/354974912613449730.svg?logo=discord&label=LSIO%20Discord&style=flat-square)](https://discord.gg/YWrKVTn)
[![](https://images.microbadger.com/badges/version/linuxserver/clarkson.svg)](https://microbadger.com/images/linuxserver/clarkson "Get your own version badge on microbadger.com")
[![](https://images.microbadger.com/badges/image/linuxserver/clarkson.svg)](https://microbadger.com/images/linuxserver/clarkson "Get your own version badge on microbadger.com")
![Docker Pulls](https://img.shields.io/docker/pulls/linuxserver/clarkson.svg)
![Docker Stars](https://img.shields.io/docker/stars/linuxserver/clarkson.svg)
[![Build Status](https://ci.linuxserver.io/buildStatus/icon?job=Docker-Pipeline-Builders/docker-clarkson/master)](https://ci.linuxserver.io/job/Docker-Pipeline-Builders/job/docker-clarkson/job/master/)
[![](https://lsio-ci.ams3.digitaloceanspaces.com/linuxserver/clarkson/latest/badge.svg)](https://lsio-ci.ams3.digitaloceanspaces.com/linuxserver/clarkson/latest/index.html)

[Clarkson](https://github.com/linuxserver/Clarkson) is a web-based dashboard application that gives you a neat and clean interface for logging your fuel fill-ups for all of your vehicles. The application has full multi-user support, as well as multiple vehicles per user. Whenever you fill-up your car or motorcycle, keep the receipt and record the data in Clarkson.

[![clarkson](https://raw.githubusercontent.com/linuxserver/Clarkson/master/docs/dashboard.png)](https://github.com/linuxserver/Clarkson)

## Supported Architectures

Our images support multiple architectures such as `x86-64`, `arm64` and `armhf`. We utilise the docker manifest for multi-platform awareness. More information is available from docker [here](https://github.com/docker/distribution/blob/master/docs/spec/manifest-v2-2.md#manifest-list). 

Simply pulling `linuxserver/clarkson` should retrieve the correct image for your arch, but you can also pull specific arch images via tags.

The architectures supported by this image are:

| Architecture | Tag |
| :----: | --- |
| x86-64 | amd64-latest |
| arm64 | arm64v8-latest |
| armhf | arm32v6-latest |


## Usage

Here are some example snippets to help you get started creating a container.

### docker

```
docker create \
  --name=clarkson \
  -e PUID=1001 \
  -e PGID=1001 \
  -e MYSQL_HOST=<mysql_host> \
  -e MYSQL_USERNAME=<mysql_username> \
  -e MYSQL_PASSWORD=<mysql_password> \
  -e ENABLE_REGISTRATIONS=<true|false> \
  -e TZ=Europe/London \
  -p 3000:3000 \
  --restart unless-stopped \
  linuxserver/clarkson
```


### docker-compose

Compatible with docker-compose v2 schemas.

```
---
version: "2"
services:
  clarkson:
    image: linuxserver/clarkson
    container_name: clarkson
    environment:
      - PUID=1001
      - PGID=1001
      - MYSQL_HOST=<mysql_host>
      - MYSQL_USERNAME=<mysql_username>
      - MYSQL_PASSWORD=<mysql_password>
      - ENABLE_REGISTRATIONS=<true|false>
      - TZ=Europe/London
    ports:
      - 3000:3000
    mem_limit: 4096m
    restart: unless-stopped
```

## Parameters

Container images are configured using parameters passed at runtime (such as those above). These parameters are separated by a colon and indicate `<external>:<internal>` respectively. For example, `-p 8080:80` would expose port `80` from inside the container to be accessible from the host's IP on port `8080` outside the container.

| Parameter | Function |
| :----: | --- |
| `-p 3000` | WebUI |
| `-e PUID=1001` | for UserID - see below for explanation |
| `-e PGID=1001` | for GroupID - see below for explanation |
| `-e MYSQL_HOST=<mysql_host>` | Points the backend to the MySQL database. This can be either a docker hostname or an IP. |
| `-e MYSQL_USERNAME=<mysql_username>` | The user with access to the _clarkson_ schema. |
| `-e MYSQL_PASSWORD=<mysql_password>` | The password for the user. |
| `-e ENABLE_REGISTRATIONS=<true|false>` | **Defaults to _false_**. If set to _true_, allows new users to register. |
| `-e TZ=Europe/London` | Specify a timezone to use EG Europe/London. |

## User / Group Identifiers

When using volumes (`-v` flags) permissions issues can arise between the host OS and the container, we avoid this issue by allowing you to specify the user `PUID` and group `PGID`.

Ensure any volume directories on the host are owned by the same user you specify and any permissions issues will vanish like magic.

In this instance `PUID=1001` and `PGID=1001`, to find yours use `id user` as below:

```
  $ id username
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```


&nbsp;
## Application Setup

**Please ensure MySQL is running before starting this container**.

It is preferred if you create the `clarkson` schema before initially running the container, then creating a user with granted permissions for the schema. Creating the schema before running the app is important as the "clarkson" user will not have permission to create the schema on your behalf. You can, of course, use the "root" user, which has the ability to create schemas automatically, but this is not recommended.

```sql
CREATE SCHEMA `clarkson`;
CREATE USER 'clarkson_user' IDENTIFIED BY 'supersecretpassword';
GRANT ALL ON `clarkson`.* TO 'clarkson_user';
```

Once running, the container will run an initial MySQL migration, which populates the schema with all tables and procedures. The application will start immediately afterwards. You will need to register an initial user, of which will be the admin of the application. All subsequent users will be standard users. You can disable registrations after the fact by recreating the container with the `ENABLE_REGISTRATIONS` flag set to `false`. This will not hide the "Register" link, but will disable the functionality.



## Support Info

* Shell access whilst the container is running: `docker exec -it clarkson /bin/bash`
* To monitor the logs of the container in realtime: `docker logs -f clarkson`
* container version number 
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' clarkson`
* image version number
  * `docker inspect -f '{{ index .Config.Labels "build_version" }}' linuxserver/clarkson`

## Updating Info

Most of our images are static, versioned, and require an image update and container recreation to update the app inside. With some exceptions (ie. nextcloud, plex), we do not recommend or support updating apps inside the container. Please consult the [Application Setup](#application-setup) section above to see if it is recommended for the image.  
  
Below are the instructions for updating containers:  
  
### Via Docker Run/Create
* Update the image: `docker pull linuxserver/clarkson`
* Stop the running container: `docker stop clarkson`
* Delete the container: `docker rm clarkson`
* Recreate a new container with the same docker create parameters as instructed above (if mapped correctly to a host folder, your `/config` folder and settings will be preserved)
* Start the new container: `docker start clarkson`
* You can also remove the old dangling images: `docker image prune`

### Via Docker Compose
* Update the image: `docker-compose pull linuxserver/clarkson`
* Let compose update containers as necessary: `docker-compose up -d`
* You can also remove the old dangling images: `docker image prune`

## Versions

* **11.02.19:** - Add pipeline logic and multi arch.
* **22.08.18:** - Rebase to alpine linux 3.8.
* **19.02.18:** - Initial Release.
