Dockerized php5.2 for running on armhf
===========================================

The main reason behind this project was to be able to run some old legacy php code on arm platforms.
Specifically it is targeted towards [Scaleway](https://scaleway.com) IaaS provider, which allows you to run cheap 
armhf hardware servers.

## Usage
In order to run this image you'll need to spawn your C1 server from docker-based image.
Next, you may build everything from scratch using sources (takes several hours to compile everything), or 
just do:
```bash
$ docker pull bananos/armbuild-php52
$ docker run -d -p 9000:9000 -v /var/www:/var/www bananos/armbuild-php52
```
Make sure that container is running as expected:

```bash
$ docker ps
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS                    NAMES
7f864a1189b0        bananos/armbuild-php52   "/opt/php52/bin/php-   11 hours ago        Up 11 hours         0.0.0.0:9000->9000/tcp   thirsty_elion       
```

Running php FastCGI server will listen on default 9000 port and will have shared volume at `/var/www`. 
To test that everything is working you may try this:

```bash
$ apt-get install nginx
$ mkdir -p /var/www/
$ echo "<?php phpinfo();?>" > /var/www/index.php
```
After installing nginx create sample virtual host at `/etc/nginx/sites-enabled/myhost.com` with following contents:

```nginx
server {
        listen 80;
        server_name myhost.com;

        location / {
          root /var/www/;
          index index.php index.html index.htm;
        }

        location ~ \.php$ {
          root /var/www/;
          fastcgi_pass 127.0.0.1:9000;
          fastcgi_index index.php;
          fastcgi_param SCRIPT_FILENAME /var/www/$fastcgi_script_name;
          include fastcgi_params;
        }
}
``` 

## Notes
1) This version of php does not include `php-fpm`, which does exist for version 5.2.17 in the form of patch. 
   The main reason for this is that it does not support ARM architecture in any way (code heavily uses x86 inline asm for performance)

2) You may easily fork and modify this image and try to build it for another platform by modifying base image `armbuild/ubuntu:15.04` 
   in `Dockerfile`, since there is nothing specific about ARM in build 
