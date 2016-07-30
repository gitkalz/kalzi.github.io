---
layout: post
title: "RDP, SSH and VNC in Web Browser"
date: "2015-09-18"
comments: true
Categories: Tools
---

[Guacamole](http://guac-dev.org) is a great tool to access RDP, SSH and VNC Connections on your LAN via HTML5 compatible browsers.

Especially useful when you have your own fenced Cloud/Home based servers and you want to access your machines from pesky corporate firewalls. Take a look below.

<div align="center">
<iframe src="https://player.vimeo.com/video/116207678" width="700" height="444" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

With release of 0.9.7, developers started supporting Docker Containers. This is a great news as it allows to setup and update Guacamole very easily.

In this post I will attempt to document my setup and install experience getting a working Docker Guacamole Containers on my Debian (jessie 8) machine.

In general, these instructions should work for any Docker compatible OS which can run `docker`.

Since my Debian does not come with Docker by default, I have setup docker by following [Docker Docs](https://docs.docker.com/installation/debian/) for Debian.

If your OS flavor is something else, just refer to Docker documentation for setup instructions.

For Debian, add docker repo to source list to install Docker client and daemon

{% highlight ruby %}
sudo echo "deb http://http.debian.net/debian jessie-backports main" > /etc/apt/sources.list
sudo apt-get update
sudo apt-get install docker.io
{% endhighlight %}

Once installed check if Docker is running as indicated below

{% highlight ruby %}
$ docker info
Containers: 0
Images: 0
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Execution Driver: native-0.2
Kernel Version: 3.16.0-4-amd64
Operating System: Debian GNU/Linux 8 (jessie)
CPUs: 2
Total Memory: 1.963 GiB
{% endhighlight %}

Now that the docker is verified running, we can now pull the required docker containers for Guacamole from Docker Hub.

To get `guacamole` working with `docker` we need three containers/components as listed

1. **_GuacD_** - Guacamole Core Daemon
2. **_MySQL_** Database
3. **_Guacamole_** Web Service to server web based requests.

It is best to open two `ssh` sessions or a `tmux` session with two windows for the reasons I will indicate later.
For the purpose of this exercise I shall be operating in `tmux` with two windows named `tmux:window1` and `tmux:window2`

If you dont have `tmux` installed, you can install `tmux` from `sudo apt-get install tmux`

Start a tmux session by entering `tmux` in your `` or `zsh` terminal.
Create a new window in `tmux` by pressing `ctrl+b c`.
Toggle between windows by pressing `ctrl+b n` or `ctrl+b p` by noticing `*`  indicated on `tmux` status bar

Following Docker commands pulls the respective containers from Docker Hub

**_[tmux:window1]_**

{% highlight ruby %}
docker pull glyptodon/guacd
docker pull glyptodon/guacamole
docker pull mysql
{% endhighlight %}
As of this writing the latest `guacd` is 0.9.8. You may check the pulled images status as below.

{% highlight ruby %}
➜  ~  docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
glyptodon/guacamole   latest              c2eb66eca17e        2 days ago          362.4 MB
glyptodon/guacd       latest              75ca55aa9180        2 days ago          404.4 MB
mysql                 latest              6762f304c834        8 days ago          283.5 MB
{% endhighlight %}

It is best to start `guacd` container first as it comes all prerubyigured and does not require any fiddling.

```
docker run --name lab-guacd -d glyptodon/guacd
```

Next we need to create a database and rubyigure guacamole schema, for this we first need to start the `MySQL` container.

```
docker run --name lab-mysql -e MYSQL_ROOT_PASSWORD=Hg6WPHtWF8cnKMuC -d mysql:latest
```
You may change SQL Root password to your preferred one, I used a random string for this temp deployment.

Now lets check if these containers are running in the background.

{% highlight ruby %}
➜  ~  docker ps -a
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS               NAMES
aaaf64af5e4c        mysql:latest             "/entrypoint.sh mysq   16 minutes ago      Up 16 minutes       3306/tcp            lab-mysql
37a9fcf41fb1        glyptodon/guacd:latest   "/usr/local/sbin/gua   18 minutes ago      Up 18 minutes       4822/tcp            lab-guacd
{% endhighlight %}

It is time to create a new database and format it for Guacamole schema on the `mysql` container running.

We can connect to running `mysql` container by linking a new temporary `mysql` container as below.

```
➜ docker run -it --link lab-mysql:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'
```


Above `docker` command will create a new `mysql` container and link it with already running `lab-mysql` container and will set us with `mysql>` prompt

{% highlight ruby %}
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.26 MySQL Community Server (GPL)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
{% endhighlight %}

It is at this `mysql>` prompt we need to create `guacamole_db` and its associated tables. Before we create database and its users, lets fetch the 'initdb.sql' schema from the `guacamole image` and push it to this temporary container.  

Pause on this Session for now and switch to a `tmux:window2` on your `tmux` or your second `ssh` session and extract the `initdb.sql` file as follows

**_[tmux:window2]_**

```
docker run --rm glyptodon/guacamole /opt/guacamole/bin/initdb.sh --mysql > /tmp/initdb.sql
```
Above operation will extract a `initdb.sql` database schema file from guacamole image and spit it to `/tmp/initdb.sql`

Now checkout the docker name for the temp `mysql` container as follows

```
➜  /tmp  docker ps -a
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS               NAMES
b8888c786e37        mysql:latest             "\"/entrypoint.sh sh   7 seconds ago       Up 6 seconds        3306/tcp            boring_colden
aaaf64af5e4c        mysql:latest             "/entrypoint.sh mysq   29 minutes ago      Up 29 minutes       3306/tcp            lab-mysql
37a9fcf41fb1        glyptodon/guacd:latest   "/usr/local/sbin/gua   32 minutes ago      Up 32 minutes       4822/tcp            lab-guacd
```

From above output, I notice that my temp `mysql` container name is `boring_colden`. I am interested to find the mapped volume for this container as I need to push the `initdb.sql` file to its volume to help me import the database schema. Following command gives me the location of `docker volume`mapped directory for `boring_colden`

{% highlight json %} 
➜ {% raw %} docker inspect -f '{{ json .Mounts }}' boring_colden{% endraw %}
[{
"Name":"7734e419c315a99d2c1be6eeba9c89cd47358dec312b2950b51f54a6b1bbd4b6",
"Source":"/var/lib/docker/volumes/7734e419c315a99d2c1be6eeba9c89cd47358dec312b2950b51f54a6b1bbd4b6/_data",
"Destination":"/var/lib/mysql",
"Driver":"local",
"Mode":"",
"RW":true
}]

➜  sudo cp /tmp/initdb.sql /var/lib/docker/volumes/7734e419c315a99d2c1be6eeba9c89cd47358dec312b2950b51f54a6b1bbd4b6/_data

➜  sudo ls /var/lib/docker/volumes/7734e419c315a99d2c1be6eeba9c89cd47358dec312b2950b51f54a6b1bbd4b6/_data
        initdb.sql
{% endhighlight %}

Pay attention to the output of `docker inspect` command and notice the `source` field, then copy the extracted `initdb.sql` to source mapped location.. Find the sample output from above.

Now you may switch back to `tmux:window1` where our temp container is waiting at 'mysql>' prompt

check out if the pushed `initdb.sql` is visible in our temp mysql container as below, if you dont see it then make sure you pushed your file correctly.

```
mysql> \! ls /var/lib/mysql
initdb.sql
```
From above command I can rubyirm that `initdb.sql` is accessible. Now I can proceed to create database and its related tables by executing the following instructions at `mysql>_` prompt

```
CREATE DATABASE guacamole_db;
CREATE USER 'guacamole_user' IDENTIFIED BY 'p4rER9hN2rvChApU';
GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user';
FLUSH PRIVILEGES;
use guacamole_db;
source /var/lib/mysql/initdb.sql;
```
> You may find better results if you put above `mysql` statements one after the other instead of copy/pasting as whole block

Above code basically creates `guacamole_db` database and creates `guacamole_user` with secure string `p4rER9hN2rvChApU`. Once the database is created, we instructed the system to use the `guacamole_db` and source the schema creation instructions from `initdb.sql`.

`initdb.sql` statements creates necessary tables, default users and permissions settings for guacamole web-client interface

Once you execute the above instructions you must verify the tables structure on `guacamole_db` as follows

```
mysql> show tables;
+---------------------------------------+
| Tables_in_guacamole_db                |
+---------------------------------------+
| guacamole_connection                  |
| guacamole_connection_group            |
| guacamole_connection_group_permission |
| guacamole_connection_history          |
| guacamole_connection_parameter        |
| guacamole_connection_permission       |
| guacamole_system_permission           |
| guacamole_user                        |
| guacamole_user_permission             |
+---------------------------------------+
9 rows in set (0.00 sec)
```

You may now exit the `mysql>_` prompt by typing `quit;`

Thats about it.. We now have a `GuacD` Core Engine running and the required `MYSQL` database created.We just need to link these running containers with `Guacamole` container using following `docker` command

```
➜  ~  docker run --name lab-guacamole --link lab-guacd:guacd \
     --link lab-mysql:mysql      \
     -e MYSQL_DATABASE=guacamole_db  \
     -e MYSQL_USER=guacamole_user    \
     -e MYSQL_PASSWORD=p4rER9hN2rvChApU \
     -d -p 80:8080 glyptodon/guacamole
```

If you do not see any errors on exection of the above command, your Docker Guacamole would be listening on `http://your_ip:80/guacamole/#/`

Default username and password is `guacadmin`.

PS: Based on your OS, you might want to start these containers automatically at startup. Create and register the respective systemd service files etc..,

----

## Troubleshooting

----
If you are noticing unexpected behaviour, you may look for log files for each container by using following `docker` commands

``` 
docker logs lab-guacamole
```
``` 
docker logs lab-guacd
```
``` 
docker logs lab-mysql
```

If you think the application is helpful, say thank you to [GuacDev](https://twitter.com/GuacDev) 
