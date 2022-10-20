I added 1 package to this docker image, becasue I needed it to be able to send remote sleep/shutdown commands my windows PC. I take no credit for all the hard work other people have done to make the actual homebridge app and docker image. 

[![Docker Build Status](https://github.com/oznu/docker-homebridge/workflows/Build/badge.svg)](https://github.com/AlecWiese/docker-homebridge-samba/actions)


# Homebridge Docker Image with Samba tools

Fork of Onzu's Ubuntu Linux based Docker image, that includes Samba tools. Allows you to run [Nfarina's](https://github.com/nfarina) [Homebridge](https://github.com/nfarina/homebridge) on your home network which emulates the iOS HomeKit API. with samba tools to allow commandline communication to a Windows computer.

This is a multi-arch image and will run on x86_64, Raspberry Pi 2, 3, 4, Zero 2 W, or other Docker-enabled ARMv7/8 devices. Docker will automatically pull the correct image for your system.

| Image Tag             | Architectures           | Base Image         | 
| :-------------------- | :-----------------------| :----------------- | 
| latest, ubuntu        | amd64, arm32v7, arm64v8 | Ubuntu 20.04       | 

## Step-By-Step Guides

- [Running Homebridge with Docker on Linux](https://github.com/homebridge/homebridge/wiki/Install-Homebridge-on-Docker)
- [Running Homebridge on a Synology NAS](https://github.com/oznu/docker-homebridge/wiki/Homebridge-on-Synology)
- [Running Homebridge on Unraid](https://github.com/oznu/docker-homebridge/wiki/Homebridge-on-Unraid)
- [Running Homebridge on TrueNas Scale](https://github.com/oznu/docker-homebridge/wiki/Homebridge-on-TrueNAS-Scale)

## Compatibility

Homebridge requires full access to your local network to function correctly which can be achieved using the ```--net=host``` flag.

**This image will not work when using [Docker for Mac](https://docs.docker.com/docker-for-mac/) or [Docker for Windows](https://docs.docker.com/docker-for-windows/) due to [this](https://github.com/docker/for-mac/issues/68) and [this](https://github.com/docker/for-win/issues/543)**.


## Usage

Command Line:

```bash
docker run --net=host --name=homebridge -v $(pwd)/homebridge:/homebridge oznu/homebridge:latest
```

Using [Docker Compose](https://docs.docker.com/compose/) (recommended):

```yml
version: '2'
services:
  homebridge:
    image: oznu/homebridge:latest
    restart: always
    network_mode: host
    volumes:
      - ./volumes/homebridge:/homebridge
    logging:
      driver: json-file
      options:
        max-size: "10mb"
        max-file: "1"
```

## Parameters

The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side.

* `--net=host` - Shares host networking with container, **required**
* `-v /homebridge` - The Homebridge config and plugin location, **required**

##### *Optional Settings:*

* `-e TZ` - for [timezone information](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) e.g. `-e TZ=Australia/Canberra`
* `-e ENABLE_AVAHI` - default is `1`; set to `0` to prevent the Avahi mDNS service running in the container

## Homebridge UI

This image comes with the [Homebridge UI](https://github.com/oznu/homebridge-config-ui-x) pre-installed and is the easiest way to manage all aspects of Homebridge.

To manage Homebridge go to `http://<ip of server>:8581` in your browser. For example, `http://192.168.1.20:8581`. From here you can install, remove and update plugins, modify the Homebridge config.json and restart Homebridge.

<p align="center">
  <img width="600px" src="https://user-images.githubusercontent.com/3979615/71886653-b16d3f80-3190-11ea-9ff8-49dc4ae4fff0.png">
</p>

## Automated Updates

Automated updates of the Homebridge Docker Image using tools such as Watchtower or similar are strongly discouraged and are done so at your own risk. You can update Homebridge, the Homebridge UI and the Node.js runtime from inside the container.

## Troubleshooting

#### 1. What's Different from the Official Image?

The official image includes ffmpeg, with `libfdk-aac` audio support. I've added samabe so you can send "net" commands to remotely control a windows computer

#### 2. Container will not start on older versions of Raspbian

If you're seeing errors like the following, your host operating system needs to be updated.

See [#434](https://github.com/oznu/docker-homebridge/issues/434) and [#441](https://github.com/oznu/docker-homebridge/issues/441) for potential solutions.

```
Node.js[445]: ../src/util.cc:188:double node::GetCurrentTimeInMicroseconds(): Assertion `(0) == (uv_gettimeofday(&tv))' failed.
Aborted (core dumped)
```

```
homebridge_1  | s6-svscan: warning: unable to iopause: Operation not permitted
homebridge_1  | s6-svscan: warning: executing into .s6-svscan/crash
homebridge_1  | s6-svscan crashed. Killing everything and exiting.
```

```
homebridge    | # Fatal error in , line 0
homebridge    | # unreachable code
```


## License

Copyright (C) 2017-2022 Alec

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the [GNU General Public License](./LICENSE) for more details.
