---
title: What is the Docker ancestor image?
date: 2018-12-15 10:38:29
tags:
---

*I am in the middle of Advent of Code 2018, a great daily programming challenge. I will also be working on the site and on more coding practice, so I may reduce my activity on the blog in the near future.*

**TL;DR** The ancestor image is `FROM scratch`, a no-op command.

I have had many opportunities to introduce Docker to my coworkers in depth, although I had never received any sort of training. Rather I relied on Internet resources.

And so I have had some difficulty answering one question that each of my coworkers would inevitably ask as I explain the layout of a Docker image.

The way I do it is simply going over a Dockerfile, such as this one which is basically the [`Dockerfile`](https://github.com/influxdata/influxdata-docker/blob/master/telegraf/1.8/alpine/Dockerfile) for [Telegraf](https://github.com/influxdata/telegraf), the metrics agent:

```dockerfile
FROM alpine:3.6
RUN echo 'hosts: files dns' >> /etc/nsswitch.conf
RUN apk add --no-cache iputils ca-certificates net-snmp-tools procps lm_sensors && \
    update-ca-certificates

ENV TELEGRAF_VERSION 1.8.3

RUN setup.sh

EXPOSE 8125/udp 8092/udp 8094

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["telegraf"]
```

As I introduce the `FROM` operator, I mention the notion of base image, which is explained [here](https://docs.docker.com/develop/develop-images/baseimages/#create-a-simple-parent-image-using-scratch). Then, the question occurs: "So what is the ancestor image? What happens if I check the parent image of the parent of the parent..." It obviously comes to a stop eventually.

Let's take the `Dockerfile` above as an example: it is built on top of [Alpine Linux](https://alpinelinux.org/), a minimal Linux distro that is very popular for building containers that use little disk space and RAM. It ships with basic utilities like BusyBox for a mere 5MB volume.

So how is Alpine Linux built? It's a bit complicated because of the automated versioning and build process, but it comes down to this: a root filesystem named `rootfs.tar.xz` is put together and then the [following Dockerfile](https://github.com/gliderlabs/docker-alpine/blob/master/versions/gliderlabs-3.8/Dockerfile) builds the Alpine Linux image:

```dockerfile
FROM scratch
ADD rootfs.tar.xz /
CMD ["/bin/sh"]
```

The key is the first line: `FROM scratch`. As of docker 1.5, [`FROM scratch`](https://docs.docker.com/samples/library/scratch/) is a no-op command. It is designed for images that do not need an actual parent image. That could be either a user space like Debian or Alpine Linux that ships with interpreters like `bash` and other utilities usually taken for granted, or just straight-up binaries to make the most minimal images possible.