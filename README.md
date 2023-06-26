Tigase's Docker package for the Tigase XMPP Server.

[![](https://images.microbadger.com/badges/version/tigase/tigase-xmpp-server:8.1.0.svg)](https://microbadger.com/images/tigase/tigase-xmpp-server:8.1.0 "Get your own version badge on microbadger.com")
![Docker Pulls](https://img.shields.io/docker/pulls/tigase/tigase-xmpp-server)
![Docker Image Size (tag)](https://img.shields.io/docker/image-size/tigase/tigase-xmpp-server/8.1.0)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Supported tags and respective `Dockerfile`s](#supported-tags-and-respective-dockerfiles)
  - [Simple tags](#simple-tags)
- [What is Tigase XMPP server?](#what-is-tigase-xmpp-server)
- [How to use this image?](#how-to-use-this-image)
  - [Starting Tigase XMPP Server](#starting-tigase-xmpp-server)
  - [Configuration](#configuration)
    - [Setting hostname](#setting-hostname)
      - [Dedicated network](#dedicated-network)
    - [Exposing ports](#exposing-ports)
    - [Connecting to external database](#connecting-to-external-database)
    - [Automatically creating Admin user](#automatically-creating-admin-user)
    - [Exported volumes](#exported-volumes)
    - [Tweaking memory configuration](#tweaking-memory-configuration)
    - [Operating system settings](#operating-system-settings)
      - [Number of opened files](#number-of-opened-files)
      - [TCP network settings](#tcp-network-settings)
  - [Complete Run Examples](#complete-run-examples)
    - [Single, basic instance](#single-basic-instance)
    - [Cluster with mysql](#cluster-with-mysql)
- [Building & Publishing](#building--publishing)
  - [Publishing](#publishing)
  - [Tagging nightlies](#tagging-nightlies)
- [License](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Supported tags and respective `Dockerfile`s

## Simple tags
-   [`nightly` (*nightly/Dockerfile*)](nightly/Dockerfile)
-   [`nightly-enterprise` (*nightly/Dockerfile*)](nightly/Dockerfile)
-   [`8.2.1`, `latest` (*8.2.1/Dockerfile*)](8.2.1/Dockerfile)
-   [`8.2.1-enterprise`, `latest-enterprise` (*8.2.1/Dockerfile*)](8.2.1/Dockerfile)
-   [`8.2.0` (*8.2.0/Dockerfile*)](8.2.0/Dockerfile)
-   [`8.2.0-enterprise` (*8.2.0/Dockerfile*)](8.2.0/Dockerfile)
-   [`8.1.2` (*8.1.2/Dockerfile*)](8.1.2/Dockerfile)
-   [`8.1.1` (*8.1.1/Dockerfile*)](8.1.1/Dockerfile)
-   [`8.1.0` (*8.1.0/Dockerfile*)](8.1.0/Dockerfile)
-   [`8.0.0` (*8.0.0/Dockerfile*)](8.0.0/Dockerfile)
-   [`8.0.0-jre8` (*8.0.0/jre-8/Dockerfile*)](8.0.0/jre-8/Dockerfile)

> **_NOTE:_** `enterprise` flavours containin commercial components

# What is Tigase XMPP server?

[Tigase XMPP Server](https://github.com/tigase/tigase-server/) is scalable and performant server implementation of the XMPP protocol written in Java.

For more information about Tigase XMPP Server and related products, please visit https://tigase.net.

Documentation for Tigase XMPP Server is available at https://docs.tigase.net.

Available from [DockerHub](https://hub.docker.com/r/tigase/tigase-xmpp-server)

![Tigase logo](docs/logo.png)

# How to use this image?

## Starting Tigase XMPP Server

Starting Tigase XMPP Server is very simple:

```bash
$ docker pull tigase/tigase-xmpp-server
$ docker run --name tigase-server -p 8080:8080 -p 5222:5222 tigase/tigase-xmpp-server:tag
```

where `tigase-server` is name of the container that will be created and `tag` is the tag specifying version of Tigase XMPP Server to run (if `tag` is not specified then `latest` will be used)

If Tigase XMPP Server is started for the first time (without any configuration), it will start web-based setup at port 8080.

## Configuration

### Setting hostname

In some cases, ie. in clustering, you may want to change hostname of the container of Tigase XMPP Server. To do so you need to add `--hostname cluster-node-1` (`-h cluster-node-1`) to the list of `docker run` parameters.

#### Dedicated network

It's often a good idea to group related docker services on same, dedicated network. First, create the network: `docker network create -d bridge tigase_cluster` and then add it to `docker run` parameters: `--network tigase_cluster`

### Exposing ports

Tigase XMPP Server as the XMPP server is only useful if accessible from the outside of the container. Tigase exposes following ports:
- `5222` - for incoming client to server XMPP connections (over StartTLS)
- `5223` - for incoming client to server XMPP connections (over DirectTLS/SSL)
- `5269` - for federated XMPP connections (s2s)
- `5277` - for inter-cluster communication
- `5280` - for BOSH connections
- `5281` - for BOSH connections over TLS/SSL
- `5290` - for WebSocket connections
- `5291` - for WebSocket connections over TLS/SSL
- `8080` - for HTTP server (web-based setup, REST API, file upload extension, etc.)
- `9050` - for JXM monitoring

Docker image defines all of the above ports as exportable, however it depends on the Tigase XMPP Server configuration if particular service is available at any of those ports.

### Connecting to external database

If you want to use Tigase XMPP Server with the external database you need to connect Tigase XMPP Server container to the database container (must be in the same docker network) or allow Tigase XMPP Server to access database server.

Tigase XMPP Server supports following databases:
- DerbyDB
- MySQL
- MSSQL
- PostgreSQL
- MongoDB

for details about required version of the databases please check Tigase XMPP Server documentation at https://docs.tigase.net/.

It is recommended to pass database username and password for creation and schema management of the database.

```bash
$ docker run -e 'DB_ROOT_USER=root' -e 'DB_ROOT_PASS=root-pass' --name tigase-server -d tigase/tigase-xmpp-server
```

This will allow Tigase XMPP Server to manage and verify database schema.

Database configuration may be then done using web-based setup.

### Automatically creating Admin user

It's possible to pass `ADMIN_JID` and `ADMIN_PASSWORD` environment variables (using `-e` parameter) to automatically create an Administrator user.

### Exported volumes

This image exports following volumes to allow you to keep configuration, logs and persistent data outside of the container:
- `/home/tigase/tigase-server/etc/` - configuration of the server *(default config files will be created after first startup of the container)*
- `/home/tigase/tigase-server/certs/` - SSL certificates for use by the server for securing connectivity
- `/home/tigase/tigase-server/logs/` - detailed logs of the server
- `/home/tigase/tigase-server/data/` - data stored by HTTP-based file upload feature of the server

> **NOTE:** It's possible (and recommended) to share `etc` configuration directory across Tigase cluster as all instances use the same configuration.

### Tweaking memory configuration

When using default Tigase's docker images JDK11 is used, which is aware about being run within (docker) container. However, one should keep in mind that default JDK's memory settings will be applied (minimum heap of 25% of memory, maximum heap of 50% of available memory - either container's [if set], or host machine). It's possible to adjust those by setting `PRODUCTION_HEAP_SETTINGS` environment variable to the desired value. For example, to configure Tigase's JVM to use 90% and start with small initial heap add following `-e 'PRODUCTION_HEAP_SETTINGS=-XX:MaxRAMPercentage=90 -Xms128m'` to `docker run`.

It's also possible to tweak garbage collector settings by setting `GC` environment variable.

### Operating system settings

When running Tigase XMPP Server in production it's essential to apply configuration outlined in [Linux Settings for High Load Systems](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#linuxhighload). In case of Tigase's Docker image this is done via parameters applied to `docker run`:
* [`--sysctl map` (Sysctl options (default map[]))](https://docs.docker.com/engine/reference/commandline/run/#configure-namespaced-kernel-parameters-sysctls-at-runtime)
* [`--ulimit ulimit` (Ulimit options (default []))](https://docs.docker.com/engine/reference/commandline/run/#set-ulimits-in-container---ulimit)

#### Number of opened files

This parameter is inherited from the host operating system and should be configred on the host. If there is a desire to adjust it then adding `--ulimit nofile=350000:350000` to the list of `docker run` parameters would do the trick

#### TCP network settings

Network configuration adjustments are mostly needed if online user status is not detected correctly after user is disconnected (or with a significant delay).

```
--sysctl "net.ipv4.tcp_keepalive_time=60" \
--sysctl "net.ipv4.tcp_keepalive_probes=3" \
--sysctl "net.ipv4.tcp_keepalive_intvl=90" \
--sysctl "net.ipv4.ip_local_port_range=1024 65000" 
```
