# Dockerized Logrotate

[![Docker Stars](https://img.shields.io/docker/stars/registry.selfdesign.org/docker/logrotate.svg)](https://hub.docker.com/r/registry.selfdesign.org/docker/logrotate/) [![Docker Pulls](https://img.shields.io/docker/pulls/registry.selfdesign.org/docker/logrotate.svg)](https://hub.docker.com/r/registry.selfdesign.org/docker/logrotate/) [![](https://badge.imagelayers.io/registry.selfdesign.org/docker/logrotate:latest.svg)](https://imagelayers.io/?images=registry.selfdesign.org/docker/logrotate:latest 'Get your own badge on imagelayers.io')

This container can crawl for logfiles and rotate them. It is a side-car container
for containers that write logfiles and need a log rotation mechanism. Just hook up some containers and define your
backup volumes.

# Make It Short

In short, this container can rotate all your Docker logfiles just by typing:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  registry.selfdesign.org/docker/logrotate
~~~~

> This will rotate all your Docker logfiles on a daily basis up to 5 times.

You want to do it hourly? Just type:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_INTERVAL=hourly" \
  registry.selfdesign.org/docker/logrotate
~~~~

> This will put logrotate on an hourly schedule.

# How To Attach to Logs

In order to attach the side-car container to your logs you have to hook your log file folders inside volumes. Afterwards
specify the folders logrotate should crawl for log files. The container attaches by default to any file ending with **.log** inside the specified folders.

Environment variable for specifying log folders: `LOGS_DIRECTORIES`. Each directory must be separated by a whitespace character.

Example:

~~~~
LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker
~~~~

Example Logrotating all Docker logfiles:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Customize Log File Ending

You can define the file endings fluentd will attach to. The container will by default crawl for
files ending with **.log**. This can be overriden and extended to any amount of file endings.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOG_FILE_ENDINGS=json xml" \
  registry.selfdesign.org/docker/logrotate
~~~~

> Crawls for file endings .json and .xml.

# Set the Log interval

Logrotate can rotate logfile according to the following intervals:

* `hourly`
* `daily`
* `weekly`
* `monthly`
* `yearly`

You can override the default setting with the environment variable `LOGROTATE_INTERVAL`.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_INTERVAL=hourly" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Set the Number of Rotations

The default number of rotations is five. Further rotations will delete old logfiles. You
can override the default setting with the environment variable `LOGROTATE_COPIES`.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_COPIES=10" \
  registry.selfdesign.org/docker/logrotate
~~~~

> Will create 10 daily logs before deleting old logs.

# Set Maximum File size

Logrotate can do additional rotates, when the logfile exceeds a certain file size. You can specifiy file size rotation
with the environment variable `LOGROTATE_SIZE`.

Valid example values:

* `100k`: Will rotate when log file exceeds 100 kilobytes.
* `100M`: Will rotate when log file exceeds 100 Megabytes.
* `100G`: Will rotate when log file exceeds 100 Gigabytes.

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_SIZE=10M" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Set Log File compression

The default logrotate setting is `nocompress`. In order to enable logfile compression
you can set the environment variable `LOGROTATE_COMPRESSION` to `compress`.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_COMPRESSION=compress" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Set the Output directory

By default, logrotate will rotate logs in their respective directories. You can
specify a directory for keeping old logfiles with the environment variable `LOGROTATE_OLDDIR`. You can specify a full or relative path.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -v $(pwd)/logs:/logs/ \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_OLDDIR=/logs" \
  registry.selfdesign.org/docker/logrotate
~~~~

> Will move old logfiles in the local directory logs/.

# Set the Cron Schedule

You can set the cron schedule independently of the logrotate interval. You can override
the default schedule with the enviroment variable `LOGROTATE_CRONSCHEDULE`.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_INTERVAL=hourly" \
  -e "LOGROTATE_CRONSCHEDULE=* * * * * *" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Log and View the Logrotate Output

You can specify a logfile for the periodical logrotate execution. The file
is specified using the environment variable `LOGROTATE_LOGFILE`. Must be a full path!

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -v $(pwd)/logs:/logs \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_INTERVAL=hourly" \
  -e "LOGROTATE_CRONSCHEDULE=* * * * * *" \
  -e "LOGROTATE_LOGFILE=/logs/logrotatecron.log" \
  registry.selfdesign.org/docker/logrotate
~~~~

> You will be able to see logrotate output every minute in file logs/logrotatecron.log

# Logrotate Commandline Parameters

You can define the logrotate commandline parameters with the environment variable LOGROTATE_PARAMETERS.

*v*: Verbose

*d*: Debug, Logrotate will be emulated but never executed!

*f*: Force

Example for a typical testrun:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -v $(pwd)/logs:/logs \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_PARAMETERS=vdf" \
  -e "LOG_FILE=/logs/cron.log" \
  registry.selfdesign.org/docker/logrotate
~~~~

> Will run logrotate with: /usr/bin/logrotate -dvf

# Logrotate Status File

Logrotate must remember when files have been rotated when using time intervals, e.g. 'daily'. The status file will be written by default to the container volume but you can specify a custom location with the environment variable LOGROTATE_STATUSFILE.

Example:

~~~~
$ docker run -d \
  -e "LOGROTATE_INTERVAL=hourly" \
  -e "LOGROTATE_CRONSCHEDULE=0 * * * * *" \
  -e "LOGROTATE_STATUSFILE=/logrotate-status/logrotate.status" \
  -e "ALL_LOGS_DIRECTORIES=/var/log" \
  -e "LOGROTATE_PARAMETERS=vf" \
  registry.selfdesign.org/docker/logrotate
~~~~

> Writes the latest status file each logrotation. Reads status files at each start.

# Log and View the Cron Output

You can specify a separate logfile for cron. The file
is specified using the environment variable `LOG_FILE`. Must be a full path!

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -v $(pwd)/logs:/logs \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_INTERVAL=hourly" \
  -e "LOGROTATE_CRONSCHEDULE=* * * * * *" \
  -e "LOG_FILE=/logs/cron.log" \
  registry.selfdesign.org/docker/logrotate
~~~~

> You will be able to see cron output every minute in file logs/cron.log


# Setting a Date Extension

With Logrotate it is possible to split files and name them by the date they were generated when used with `LOGROTATE_CRONSCHEDULE`. By setting `LOGROTATE_DATEFORMAT` you will enable the Logrotate `dateext` option.

The default Logrotate format is `-%Y%m%d`, to enable the defaults `LOGROTATE_DATEFORMAT` should be set to this.

Example:

~~~~
$ docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGROTATE_INTERVAL=daily" \
  -e "LOGROTATE_CRONSCHEDULE=0 * * * * *" \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_DATEFORMAT=-%Y%m%d" \
  registry.selfdesign.org/docker/logrotate
~~~~

# Disable Auto Update

With Logrotate by default it auto update its logrotate configuration file to ensure it only captures all the intended log file in the `LOGS_DIRECTORIES` (before it rotates the log files). It is possible to disable auto update when used with `LOGROTATE_AUTOUPDATE`. By setting `LOGROTATE_AUTOUPDATE` (to not equal true) you will disable the auto update of Logrotate.

The default `LOGROTATE_AUTOUPDATE` is `true`, to disable the defaults `LOGROTATE_AUTOUPDATE` should be set not equal `true`.

Example:

~~~~
docker run -d \
  -v /var/lib/docker/containers:/var/lib/docker/containers \
  -v /var/log/docker:/var/log/docker \
  -e "LOGS_DIRECTORIES=/var/lib/docker/containers /var/log/docker" \
  -e "LOGROTATE_AUTOUPDATE=false" \
  registry.selfdesign.org/docker/logrotate
~~~~


# References

* [Logrotate](http://www.linuxcommand.org/man_pages/logrotate8.html)
