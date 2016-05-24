Dockerized mysql5.1
======================
## Why?
If you need to run some legacy which absolutely requires old MySQL versions.

## Usage

Next, you may build everything from scratch using sources (takes ~ 1 hour to compile everything), or 
just do:
```bash
$ docker pull bananos/mysql51
$ docker run -d -p 3306:3306 -v /mnt/data:/var/mysql51/data -v /mnt/logs:/var/log/mysql51 -e MYSQL_ROOT_PASSWORD=12345 bananos/mysql51
```
On the first run initial database files will be created (via `mysql_install_db`) and root password set.

Make sure that container is running as expected:

```bash
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                CREATED             STATUS              PORTS                    NAMES
8cfa9008067d        bananos/mysql51   "/entrypoint.sh mysq   3 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   gloomy_wozniak  
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
