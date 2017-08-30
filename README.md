# hub.docker.com/tiredofit/logrotate

# Introduction

Dockerfile to build an [logrotate](http://www.linuxcommand.org/man_pages/logrotate8.html) container image.
This will allow you to rotate all your containers logs, and optionally compress them.

* This Container uses a [customized logrotate Linux base](https://hub.docker.com/r/tiredofit/alpine) which includes [s6 overlay](https://github.com/just-containers/s6-overlay) enabled for PID 1 Init capabilities, [zabbix-agent](https://zabbix.org) based on TRUNK compiled for individual container monitoring, Cron also installed along with other tools (bash,curl, less, logrotate, mariadb-client, nano, vim) for easier management. It also supports sending to external SMTP servers..


# Authors

- [Dave Conroy](dave at tiredofit dot ca) [https://github.com/tiredofit]

# Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
    - [Data Volumes](#data-volumes)
    - [Environment Variables](#environmentvariables)   
    - [Networking](#networking)
- [Maintenance](#maintenance)
    - [Shell Access](#shell-access)
   - [References](#references)

# Prerequisites

No prequisites required

# Installation

Automated builds of the image are available on [Docker Hub](https://hub.docker.com/tiredofit/logrotate) and 
is the recommended method of installation.


```bash
docker pull tiredofit/logrotate:(imagetag)
```

# Quick Start

See the included `docker-compose.yml` file for a working example on how to quickly rotate your logs.

# Configuration

### Data-Volumes

Map your directories you wish to rotate, otherwise use the following:

| Parameter         | Description                                                    |
|-------------------|----------------------------------------------------------------|
| `/var/lib/docker/containers:/var/lib/docker/containers` | Will crawl through all running containers |
| `/var/log/docker:/var/log/docker` | Will compress the Docker json container logs |


### Environment Variables

Along with the Environment Variables from the [Base image](https://hub.docker.com/r/tiredofit/alpine), below is the complete list of available options that can be used to customize your installation.

Below is the complete list of available options that can be used to customize your installation.

| Parameter         | Description                                                    |
|-------------------|----------------------------------------------------------------|

| `LOG_FILE_ENDINGS` | Which extensions to seach for logs e.g. `json xml log` |
| `LOGROTATE_INTERVAL` | How often to rotate e.g. `hourly` `daily` `weekly` `monthly` `yearly` |
| `LOGROTATE_COPIES` | How many copies to keep before deleting e.g. `8` |
| `LOGROTATE_SIZE` | Rotate when log reaches this level e.g. `10M`. Use number plus `K` `M` or `G` |
| `LOGROTATE_COMPRESSION` | Should be compress logs e.g. `compress`. Set to `nocompress` otherwise |
| `LOGROTATE_DATEFORMAT` | How to name the rotated logs e.g. `-%Y%m%d` |
| `LOGROTATE_OLDDIR` | Where to place old logs. Default is same directory |

# Maintenance
#### Shell Access

For debugging and maintenance purposes you may want access the containers shell. 

```bash
docker exec -it (whatever your container name is e.g. logrotate) bash
```

# References

* http://www.linuxcommand.org/man_pages/logrotate8.html
* https://www.logrotatelinux.org

