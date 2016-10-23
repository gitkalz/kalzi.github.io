---
layout: post
title: "Linux Terminal in a Web Browser"
date: "2015-10-06"
comments: true
categories: Tools
---

The other day I was looking to access my home lab securely from any location without installing VPN's or other network tunneling hassle. I was interested if I could get a fully capable terminal from a web-browser over SSL. While [Guacamole](http://kalzi.github.io/2015/guacamole-with-docker-containers/) served my purpose to access remote servers via RDP and SSH over a web browser, I am not quite satisfied with how the terminal rendered. 

In search of my requirement I came across a creation from Mr.Christopher Jeffrey in the name of `tty.js` which is purely built on Javascript and emulates `XTERM`. Chris's [GIT Repo](https://github.com/chjj/tty.js) has a ready to start web service that can be run on a `node.js` server.

![alt text](http://i.imgur.com/laD3d5f.png "Terminal Example")

Since, my goal is to package services into immutable docker containers that can readily be pulled and deployed in short time, I have set out to build a customized docker container of `tty.js` service with `ZSH` and awesome `Prezto` prompt customizations.

The result is now published to [Docker Hub](https://hub.docker.com/explore/) as  `kalz/zsh_tty` docker image.

You may wish to use my docker image or build your own by customizing my [GIT Repo](https://github.com/kalzi/mytty) `Dockerfile` per your liking.

Check out the [terminal recording](https://asciinema.org/a/4qktrmlwxbhdl87vizno30ei1) to glance on how to build from my repo as is.

<div id="player"></div>
<script>
  src="/_includes/js/asciinema-player.js">
</script>
<asciinema-player src="/images/terminalbrowser.json"></asciinema-player>

Alternatively, you may readily use my automated image linked to my Git Repo from Docker hub as follows

1. Make sure you have docker installed and running `docker version`
2. `docker pull kalz/zsh_tty` to pull the image from docker hub
3. Start the container by running `docker run -p <portofyourchoice>:8080 --hostname <preferredhostname> --name <<nameyourcontainer> -d kalz/zsh_tty`


In my case I ran `docker run -p 8080:8080 --hostname bastion --name term -d kalz/zsh_tty`


Access your Terminal from latest web browser on `http://<dockerhost>:<portofyourchoice>/`

I have setup my Reverse Proxy `Nginx` with Basic Auth over SSL to this `location` and serve my Terminal!

One caveat is, Copy and Paste may not work in all browsers, however, the fool proof method is to use `tmux` style key modes to copy/paste as listed below.

###CheatSheet

1. `Ctrl + a` enters `prefix` mode, and in this mode you may use `[` to start `selection` mode, from here you may position your cursor to desired place using `h/j/k/l` keys and hit `v` or `space` to mark selection using `h/j/k/l` keys. Hit `Ctrl + c` to copy selected text.
2. Typically you would select a text using a mouse and right-click `copy` and hit `Ctrl+a` and `Ctrl + v` to paste the copied content.
3. When you are in `Visual` or `Selection` mode, hitting `ESC` will return you to normal mode
4. `Shift + PageUp` or `Shift + PageDown` scrolls the screen content.
