Dockerized php5.2
==========================================
## Why?
Running some old legacy code.


## Usage
Next, you may build everything from scratch using sources (takes several hours to compile everything), or 
just do:
```bash
$ docker pull bananos/php52
$ docker run -d -p 9000:9000 -v /var/www:/var/www bananos/php52
```
Make sure that container is running as expected:

```bash
$ docker ps
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS                    NAMES
7f864a1189b0        bananos/php52   "/opt/php52/bin/php-   11 hours ago        Up 11 hours         0.0.0.0:9000->9000/tcp   thirsty_elion       
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


##PECL
The following packages are build and may be configured in `php.ini`:

```
/opt/php52/bin/pecl list
Installed packages, channel pecl.php.net:
=========================================
Package    Version State
APC        3.1.13  beta
memcache   2.2.7   stable
mongo      1.5.8   stable
oauth      1.2.3   stable
redis      2.2.7   stable
timezonedb 2015.7  stable
xdebug     2.0.5   stable
zip        1.13.1  stable
```
