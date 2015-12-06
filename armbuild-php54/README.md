Dockerized php5.4 for running on armhf
===========================================

The main reason behind this project was to be able to run some old legacy php code on arm platforms.
Specifically it is targeted towards [Scaleway](https://scaleway.com) IaaS provider, which allows you to run cheap 
armhf hardware servers.

## Usage
In order to run this image you'll need to spawn your C1 server from docker-based image.
Next, you may build everything from scratch using sources (takes several hours to compile everything), or 
just do:
```bash
$ docker pull bananos/armbuild-php54
$ docker run -d -p 9000:9000  -v /var/www:/var/www bananos/armbuild-php54
```
Make sure that container is running as expected:

```bash
0$ docker ps
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS                    NAMES
7f864a1189b0        bananos/armbuild-php54   "/opt/php54/sbin/php-   11 hours ago        Up 11 hours         0.0.0.0:9000->9000/tcp   thirsty_elion       
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
          include fastcgi_params;
          fastcgi_param SCRIPT_FILENAME /var/www/$fastcgi_script_name;
        }
}
``` 


##PECL
The following packages are build and may be configured in `php.ini`:

```
/opt/php54/bin/pecl list
INSTALLED PACKAGES, CHANNEL PECL.PHP.NET:
=========================================
PACKAGE    VERSION STATE
APC        3.1.13  beta
amqp       1.6.1   stable
geoip      1.0.8   stable
memcache   2.2.7   stable
mongo      1.6.12  stable
oauth      1.2.3   stable
redis      2.2.7   stable
timezonedb 2015.7  stable
xdebug     2.3.3   stable
zip        1.13.1  stable
```


## Notes
1) You may easily fork and modify this image and try to build it for another platform by modifying base image `armbuild/ubuntu:15.04` 
   in `Dockerfile`, since there is nothing specific about ARM in build 
