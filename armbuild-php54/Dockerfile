FROM armbuild/ubuntu:15.04

RUN mkdir -p /tmp/build

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
	nano \
        wget curl \
	libbz2-dev \
	libncurses5-dev \
	autoconf \
	build-essential \
	libxml2-dev \ 
        zlibc zlib1g-dev \
	libgeoip-dev geoip-database geoip-bin geoip-dbg geoip-database-extra \
 	libpq-dev librabbitmq-dev


cd /tmp/build && wget "http://zlib.net/zlib-1.2.8.tar.gz" && tar -xf zlib-1.2.8.tar.gz && cd zlib-1.2.8 && ./configure --prefix=/opt/zlib && make -j4 && make install

# php5.4
RUN cd /tmp/build && wget "http://fr2.php.net/get/php-5.4.45.tar.bz2/from/this/mirror" -O php-5.4.45.tar.bz2 && tar -xf php-5.4.45.tar.bz2 

# build;
RUN cd /tmp/build/php-5.4.45 && ./configure --prefix=/opt/php54 --enable-fpm --with-config-file-path=/opt/php54/conf/ --with-zlib --with-zlib-dir=/opt/zlib/ --with-bz2 --with-gd --with-curl --with-openssl --with-gettext --with-curlwrappers --enable-cli --enable-gd-native-ttf --enable-ftp --enable-mbstring --enable-xml --enable-shmop  --enable-sysvsem --enable-inline-optimization --enable-mbregex --enable-sockets --with-mysql --with-pdo-mysql --with-pgsql --with-pdo-pgsql

# create php-fpm pool user/group
RUN groupadd www && useradd -g www www

# PECL extensions
RUN printf "\n" | /opt/php54/bin/pecl install memcache apc zip timezonedb geoip redis oauth xdebug-2.3.3 mongo-1.6.12 amqp-1.6.1


# conf
RUN rm -rf /tmp/*
ADD php.ini /opt/php54/conf/php.ini 
ADD php-fpm.conf /opt/php54/conf/php-fpm.conf


WORKDIR /opt

CMD ["/opt/php54/sbin/php-fpm", "--fpm-config", "/opt/php54/conf/php-fpm.conf", "-c", "/opt/php54/conf/", "--nodaemonize"]

# run php-fpm
EXPOSE 9000

