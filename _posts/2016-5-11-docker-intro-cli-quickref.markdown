---
layout: post
title: "Docker Intro and CLI QuickRef"
date: "2016-05-11"
comments: true
categories: Ref
---

Containerazation in general is bringing new possibilities to IT Services landscape. Process isolation, cgroups and lxc technologies have paved way for emerging vendor tools like Docker,Rocket and others to provide easy interfaces and packaged delivery of services. Containers can be used to rapidly deploy many isolated applications with a notion of running single application per container on aany given docker host. 

In this context, Let's attempt to know more about **Docker**

Docker is a peice of software which can be installed on an Operating System. Docker to a minimum, is made of two pieces, one the _Docker Engine_ which runs as a deamon/service and two the _Docker Client_ (`docker`), a standalone exectuable which talks to Docker Service via an API and manages the containers. Docker historically has been made popular natively on *nix/linux and Microsoft is bringing Docker containers to Windows Server 2016 onwards.

Unlike Virtual Machines, Docker can only run Unix compatible containers on Unix Docker Hosts and Windows containers in Windows Server Docker/Container host due to sharing of kernel libraries.

Think to note is Docker is not similar to Virtualization, which typically uses a Hypervisor.. Docker uses Process Isolation techniques to make applications use a dedicated process address space parellel to the host OS.

Docker operates on things called `Images` . Images typically contain a packaged application like `nginx`, `nodejs` or anything you can build of your choice. These images typically must be built to run a single process which shall serve your application.  In general, public docker Images can be downloaded from a docker repository at `hub.docker.com` or a private repository hosted on premise/cloud. `docker` client command line tool allows building of these custom containers from *Base images* .

Base Images are generally bare OS or relative services from which you'd typically build your container from. One exciting feature of Docker is that the way docker constructs these containers. It uses something called as __Union FileSystem__ with layered approach. Which means, each change you add on to your container at the build time can be saperated into layers. When storing/updating/retreiving these containers, only updated/modified layers are pushed/pulled.

You'll typically download(`pull`) a dockerized image, and `run` the image(aka container) which runs your application inside a container and `EXPOSE` the ports/services from that container to the base host to access.

Docker, like virtual machine networking, creates a OS Internal Brige/Switch where all the running containers are connected to. It is important to expose your service ports through the docker to access them beyond the host networking. 

More detailed understanding of Docker can be found at docker docs or [HERE](http://etherealmind.com/basics-docker-containers-hypervisors-coreos/)

Some of the docker commands for quick reference

##### Installation
---
Quick and easy install scripted installation on Linux based systems

```curl -sSL https://get.docker.com/ | sh```

##### Container Image Operations
---
|                        Command                       |                                   Description                                   |
|:----------------------------------------------------:|:-------------------------------------------------------------------------------:|
|                     `docker images`                    |                      _Lists images that are downloaded locally_                 |
|                 `docker search <image>`                |                   _Searches for the image in docker   registry_                 |
|                 `docker rmi <imagetag>`                |             _Deletes/untags image specified  from local filesystem_            |
|                `docker pull <imagename>`               |                   _Pulls stated image from docker  repository_                 |
|              `docker build -t <imagetag> .`              |  _Builds Docker Image from the   **Dockerfile** found in the current directory_ |
|                     `docker login`                     |               _Prompts for login to Docker Hub for image uploads_             |
|                `docker push <imagetag>`                |                        _Pushes local image to Docker Hub_                       |
|          `docker tag <imageid> <definetag>`          |             _Tags an image with the name you specify in definetag_            |
|               `docker history <imageid>`               |                          _List image operations history_                        |
|              `docker export <imagetag>`               |                   _Exports the specified image to a tarball_                  |
|                `docker import <file>`                |                  _Imports the specified image from a tarball_                 |
|                  `docker load <file>`                  |                       _loads an image from a tar archive_                     |
|                      `docker save`                     |         _Saves a image to a tar archive with all layers for later load_       |
| `docker images -qf dangling=true` &#124; `xargs docker rmi` |                _Removes all dangling images with image tag none_              |

##### Docker Service Ops
---
|                        Command                       |                                   Description                                   |
|:----------------------------------------------------:|:-------------------------------------------------------------------------------:|
|           `docker info`     |  _List the Docker service info_ |
|   `docker --version`        |     _Shows docker version_      |


##### Container Run State operations
---
|                        Command                       |                                   Description                                   |
|:----------------------------------------------------:|:-------------------------------------------------------------------------------:|
`docker run -it --name=<containername> --rm <imagename> bash`   |        _Creates and start a container interactively with TTY attached, name <containername> and from image <imagename>, removes the image after exit._
    `docker create`     |        _Creates a writeable container layer over the specified image and prepares it for running the specified command_
 `docker rename`        |       _Allows a container to be renamed_
 `docker stop <containername>` &#124; `<id>` | _Stops a container_
 `docker start <containername>` &#124; `<id>` | _Start a Stopped or Created Container_
 `docker kill <containername>` &#124; `<id>` | _Force kills a Running Container_
 `docker pause` |  _Pauses/Freezes a Container_
 `docker unpause` | _UnPauses/Runs a Paused Container_
 `docker rm <containername>` |  _Deletes a container from runstate_
 `docker ps -q` &#124; `xargs docker rm` |  _Removes all stopped Docker Containers_


##### Container Admin Ops
---
|                        Command                       |                                   Description                                   |
|:----------------------------------------------------:|:-------------------------------------------------------------------------------:|
 `docker ps -a` | _List all containers in CREATE/STOPPED/RUNNING States_
 `docker logs -f <containername>` | _Shows logs from the container, -f continuous tail_
 `docker inspect <containername>` | _Shows the detailed container metadata in JSON Format_
 `docker events <containername>` | _Shows events from Container_
 `docker port <containername>`  | _Shows public/exposed ports from the container_
 `docker top <containername>` | _Shows running process in a container_
 `docker stats <containername>` | _Shows containers resource usage statistics_
 `docker network ls` | _Shows Docker networks_
 `docker network create` | _Create a new Docker bridge_
 `docker network rm` | _Removes specified network bridge_
 `docker network inspect` | _Shows bridge IP subnet and the gateway info and associates containers attached_