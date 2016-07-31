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

| Command                                            | Description                                                            |
|----------------------------------------------------|------------------------------------------------------------------------|
| `docker images`                                      | Lists images that are downloaded locally                               |
| `docker search <image>`                              | Searches for the image in docker registry                              |
| `docker rmi <imagetag>`                              | Deletes/untags image specified from local filesystem                   |
| `docker pull <imagename>`                            | Pulls stated image from docker repository                              |
| `docker build -t <imagetag> .`                       | Builds Docker Image from the Dockerfile found in the current directory |
| `docker login`                                       | Prompts for login to Docker Hub for image uploads                      |
| `docker push <imagetag>`                             | Pushes local image to Docker Hub                                       |
| `docker tag <imageid> <definetag>`                   | Tags an image with the name you specify in definetag                   |
| `docker history <imageid>`                           | List image operations history                                          |
| `docker export <imagetag>`                           | Exports the specified image to a tarball                               |
| `docker import <file>`                               | Imports the specified image from a tarball                             |
| `docker load <file>`                                 | loads an image from a tar archive                                      |
| `docker save`                                        | Saves a image to a tar archive with all layers for later load          |
| `docker images -qf dangling=true` &#124; `xargs docker rmi` | Removes all dangling images with image tag none                 |

##### Docker Service Ops
---

| Command            | Description                  |
|--------------------|------------------------------|
| `docker info`      | List the Docker service info |
| `docker --version` | Shows docker version         |


##### Container Run State operations
---

|                           Command                           | Description                                                                                                        |
|:-----------------------------------------------------------:|--------------------------------------------------------------------------------------------------------------------|
| `docker run -it --name=<containername> --rm <imagename> bash` | Creates and start a container interactively with TTY attached, name and from image , removes the image after exit. |
| `docker create`                                               | Creates a writeable container layer over the specified image and prepares it for running the specified command     |
| `docker rename`                                               | Allows a container to be renamed                                                                                   |
| `docker stop <containername>` &#124; `<id>`                           | Stops a container                                                                                                  |
| `docker start <containername>` &#124; `<id>`                          | Start a Stopped or Created Container                                                                               |
| `docker kill <containername>` &#124; `<id>`                           | Force kills a Running Container                                                                                    |
| `docker pause`                                                | Pauses/Freezes a Container                                                                                         |
| `docker unpause`                                              | UnPauses/Runs a Paused Container                                                                                   |
| `docker rm <containername>`                                   | Deletes a container from runstate                                                                                  |
| `docker ps -q` &#124; `xargs docker rm`                              | Removes all stopped Docker Containers                                                                              |

##### Container Admin Ops
---

|             Command            | Description                                                                    |
|:------------------------------:|--------------------------------------------------------------------------------|
| docker ps -a                   | List all containers in CREATE/STOPPED/RUNNING States                           |
| docker logs -f <containername> | Shows logs from the container, -f continuous tail                              |
| docker inspect <containername> | Shows the detailed container metadata in JSON Format                           |
| docker events <containername>  | Shows events from Container                                                    |
| docker port <containername>    | Shows public/exposed ports from the container                                  |
| docker top <containername>     | Shows running process in a container                                           |
| docker stats <containername>   | Shows containers resource usage statistics                                     |
| docker network ls              | Shows Docker networks                                                          |
| docker network create          | Create a new Docker bridge                                                     |
| docker network rm              | Removes specified network bridge                                               |
| docker network inspect         | Shows bridge IP subnet and the gateway info and associates containers attached |