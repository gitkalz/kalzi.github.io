---
layout: post
title: "Docker Intro and CLI QuickRef"
date: "2016-05-11"
comments: true
categories: Ref
---

Containerazation in general is bringing new possibilities to IT Services landscape. Process isolation and cgroups and lxc technologies have paved way for emerging vendor tools like Docker,Rocket and others to provide interfaces and packaged delivery of services. Containers can be used to rapidly deploy many isolated applications on a single computer system. In this context, Let's attempt to acquaint with **Docker**

Docker is a Software which you shall install on a Linux OS. Docker to a minimum is made of two pieces, one the Docker Engine which runs as a deamon/service and two the Docker Client (`docker`), a standalone exectuable which talks to Docker Service via an API and manages the containers. Docker historically has been made popular natively on *nix/linux and Microsoft is bringing Docker containers to Windows Server 2016 onwards. Thing to note, in contrast to Virtual Machines, Docker can run Unix compatible containers on Unix Docker Hosts and Windows containers in Windows Server Docker/Container host. It appears like Windows Containers are just tool compatible with Docker client/api for management and is not exactly is a "Docker engine" running beneath.

Docker operates on containers called `Images` . These Images can be downloaded from a Docker repository at `hub.docker.com` or a private repository hosted on premise/cloud. Docker tool allows building of these custom containers from *Base images* . Base Images are generally bare OS or relative services from which you'd typically build your container from. One exiting feature of Docker is that the way docker constructs the containers. It uses something called as Union FileSystem with layered approach. Which means, each change you add on to your container at the build time can be saperated into layers. When storing/updating/retreiving these containers, only updated layers are pushed/pulled.

You typically download(`pull`) a docker image, and `run` the image(aka container) and `EXPOSE` the ports/services from that container to the base host. 

Docker, like virtual machine networking, creates a OS Internal Brige/Switch where all the running containers are connected to. It is neccessary to expose your service ports through the docker to access them beyond the host networking. 

More detailed understanding of Docker can be found [HERE](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)

Some of the docker commands for quick reference
---

|                        Command                       |                                   Description                                   |
|:----------------------------------------------------:|:-------------------------------------------------------------------------------:|
|                     `docker images`                    |                      _Lists images that are downloaded locally_                 |
|                 `docker search <image>`                |                   _Searches for the image in docker   registry_                 |
|                 `docker rmi <imagetag>`                |             _Deletes/untags image specified  from local filesystem_            |
|                `docker pull <imagename>`               |                   _Pulls stated image from docker  repository_                 |
|              `docker build -t <imagetag>`              |  _Builds Docker Image from the   **Dockerfile** found in the current directory_ |
|                     `docker login`                     |               _Prompts for login to Docker Hub for image uploads_             |
|                `docker push <imagetag>`                |                        _Pushes local image to Docker Hub_                       |
|          `docker tag <imageid> <definetag>`          |             _Tags an image with the name you specify in definetag_            |
|               `docker history <imageid>`               |                          _List image operations history_                        |
|              `docker export <imagetag>`               |                   _Exports the specified image to a tarball_                  |
|                `docker import <file>`                |                  _Imports the specified image from a tarball_                 |
|                  `docker load <file>`                  |                       _loads an image from a tar archive_                     |
|                      `docker save`                     |         _Saves a image to a tar archive with all layers for later load_       |
| `docker images -qf dangling=true | xargs   docker rmi` |                _Removes all dangling images with image tag none_              |

##### Installation

Quick and easy install scripted installation on Linux based systems

```curl -sSL https://get.docker.com/ | sh```

##### Docker Service Ops
- `docker info` _List the Docker service info_
- `docker --version` _Shows docker version_

##### Container Image Operations

- `docker images`  _Lists images that are downloaded locally_
- `docker search <image>` _Searches for the image in docker registry_
- `docker rmi <imagetag>` _Deletes/untags image specified from local filesystem_
- `docker pull <imagename>` _Pulls stated image from docker repository_
- `docker build -t <imagetag> .` _Builds Docker Image from the **Dockerfile** found in the current directory_
- `docker login` _Prompts for login to Docker Hub for image uploads_
- `docker push <imagetag>` _Pushes local image to Docker Hub_
- `docker tag <imageid> <definetag>` _Tags an image with the name you specify in definetag_
- `docker history <imageid>` _List image operations history_
- `docker export <imagetag>` _Exports the specified image to a tarball_
- `docker import file|URL` _Imports the specified image from a tarball_
- `docker load <file>` _loads an image from a tar archive_
- `docker save` _Saves a image to a tar archive with all layers for later load_
- `docker images -qf dangling=true | xargs docker rmi` _Removes all dangling images with image tag none_

---

##### Container Run State operations

- `docker run -it --name=<containername> --rm <imagename> bash` _Creates and start a container interactively with TTY attached, name <containername> and from image <imagename>, removes the image after exit._
- `docker create` _Creates a writeable container layer over the specified image and prepares it for running the specified command_
- `docker rename` _Allows a container to be renamed_
- `docker stop <containername>|<id>` _Stops a container_
- `docker start <containername>|<id>` _Start a Stopped or Created Container_
- `docker kill <containername>|<id>` _Force kills a Running Container_
- `docker pause` _Pauses/Freezes a Container_
- `docker unpause` _UnPauses/Runs a Paused Container_
- `docker rm <containername>` _Deletes a container from runstate_
- `docker ps -q |xargs docker rm` _Removes all stopped Docker Containers_

---

##### Container Admin Ops

- `docker ps -a` _List all containers in CREATE/STOPPED/RUNNING States_
- `docker logs -f <containername>` _Shows logs from the container, -f continuous tail_
- `docker inspect <containername>` _Shows the detailed container metadata in JSON Format_
- `docker events <containername>` _Shows events from Container_
- `docker port <containername>` _Shows public/exposed ports from the container_
- `docker top <containername>` _Shows running process in a container_
- `docker stats <containername>` _Shows containers resource usage statistics_
- `docker network ls` _Shows Docker networks_
- `docker network create` _Create a new Docker bridge_
- `docker network rm` _Removes specified network bridge_
- `docker network inspect` _Shows bridge IP subnet and the gateway info and associates containers attached_

---
