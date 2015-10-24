Dockerized mysql5.1 for running on armhf
===========================================

The main reason behind this project was to be able to run some old legacy php code on arm platforms.
Specifically it is targeted towards [Scaleway](https://scaleway.com) IaaS provider, which allows you to run cheap 
armhf hardware servers.

## Usage
In order to run this image you'll need to spawn your C1 server from docker-based image.
Next, you may build everything from scratch using sources (takes ~ 1 hour to compile everything), or 
just do:
```bash
$ docker pull bananos/armbuild-mysql51
$ docker run -d -p 3306:3306 -v /mnt/data:/var/mysql51/data -v /mnt/logs:/var/log/mysql51 -e MYSQL_ROOT_PASSWORD=12345 bananos/armbuild-mysql51
```
On the first run initial database files will be created (via `mysql_install_db`) and root password set.

Make sure that container is running as expected:

```bash
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                CREATED             STATUS              PORTS                    NAMES
8cfa9008067d        bananos/armbuild-mysql51   "/entrypoint.sh mysq   3 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   gloomy_wozniak  
```

Connect to mysql server:

```bash
$ mysql --user=root --password=12345 --host=127.0.0.1 --port=3306
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.1.71-innodb-plugin-log Source distribution

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> exit
Bye

```

## Notes
1) You may easily fork and modify this image and try to build it for another platform by modifying base image `armbuild/ubuntu:15.04` 
   in `Dockerfile`, since there is nothing specific about ARM in build 
