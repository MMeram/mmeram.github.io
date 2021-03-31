---
layout: post
title: "How to move docker's default /var/lib/docker to another directory on Ubuntu/Debian Linux"
date: 2021-03-25
---

The following config will guide you through a process of changing  the docker's default */var/lib/docker* storage disk space to another directory. There are various reasons why you may want to change docker's default directory from which the most obvious could be that ran out of disk space. The following guide should work for both Ubuntu and Debian Linux or any other systemd system. Make sure to follow this guide in the exact order of execution.

Let's get started by modifying systemd's docker start up script. 

Open file */lib/systemd/system/docker.service* with your
favorite text editor and replace the following line where /new/path/docker is a location of your new chosen docker directory:


>FROM:
>ExecStart=/usr/bin/docker daemon -H fd://

>TO:
>ExecStart=/usr/bin/docker daemon -g /new/path/docker -H fd://


When ready stop docker service:
>`$ sudo systemctl stop docker`

It is important here that you have completely stopped docker daemon. The following linux command will yield no output only if docker service is stopped:
>`$ ps aux | grep -i docker | grep -v grep`

If no output has been produced by the above command, reload systemd daemon:
>`$ systemctl daemon-reload`

Once this is done create a new directory you specified above and optionally rsync current docker data to a new directory:
>`$ mkdir /new/path/docker`
>`$ rsync -aqxP /var/lib/docker/ /new/path/docker`

At this stage we can safely start docker daemon:
>`$ systemctl start docker`

Confirm that docker runs within a new data directory:
> *root      2257  0.0  0.6 1804528 108052 ?      Ssl  Mär25   0:53 /usr/bin/dockerd -g /new/path/docker -H fd:// --containerd=/run/containerd/containerd.sock*


