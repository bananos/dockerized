Dockerized php5.4 
======================
## Why?
Running old legacy code.


## Usage
Next, you may build everything from scratch using sources (takes several hours to compile everything), or 
just do:
```bash
$ docker pull bananos/php54
$ docker run -d -p 9000:9000  -v /var/www:/var/www bananos/php54
```
Make sure that container is running as expected:

```bash
0$ docker ps
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS                    NAMES
7f864a1189b0        bananos/php54   "/opt/php54/sbin/php-   11 hours ago        Up 11 hours         0.0.0.0:9000->9000/tcp   thirsty_elion       
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
