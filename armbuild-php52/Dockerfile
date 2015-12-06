FROM armbuild/ubuntu:15.04

RUN mkdir -p /tmp/build
ADD patches /tmp/build/patches

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
 	libpq-dev

# zlib
RUN cd /tmp/build && wget "http://zlib.net/zlib-1.2.8.tar.gz" && tar -xf zlib-1.2.8.tar.gz && cd zlib-1.2.8 && ./configure --prefix=/opt/zlib && make -j4 && make install

# libjpeg
RUN cd /tmp/build && wget "http://www.ijg.org/files/jpegsrc.v8d.tar.gz" && tar -xf jpegsrc.v8d.tar.gz && cd jpeg-8d && ./configure --prefix=/opt/libjpeg && make -j4 && make install

# libpng
RUN cd /tmp/build && wget "http://downloads.sourceforge.net/project/libpng/libpng16/1.6.18/libpng-1.6.18.tar.gz" && tar -xf libpng-1.6.18.tar.gz && cd libpng-1.6.18 && ./configure --prefix=/opt/libpng && make -j4 && make install

# CURL
RUN cd /tmp/build && wget "http://curl.haxx.se/download/curl-7.30.0.tar.gz" && tar -xf curl-7.30.0.tar.gz && cd curl-7.30.0 && ./configure --prefix=/opt/curl && make -j4 && make install

# openssl 
RUN cd /tmp/build && curl "https://www.openssl.org/source/old/0.9.x/openssl-0.9.8zf.tar.gz" > openssl-0.9.8zf.tar.gz && tar -xf openssl-0.9.8zf.tar.gz && cd openssl-0.9.8zf && ./config --prefix=/opt/openssl && make -j4 && make install

# gettext
RUN cd /tmp/build && wget ftp://ftp.gnu.org/gnu/gettext/gettext-0.19.6.tar.gz && tar -xf gettext-0.19.6.tar.gz && cd gettext-0.19.6 && ./configure --prefix=$PREFIX/gettext --disable-dependency-tracking --disable-debug --without-included-gettext --without-included-glib --without-included-libcroco --without-included-libxml --without-emacs --without-git --without-cvs && make -j4 && make install

# freetype
RUN cd /tmp/build && wget http://downloads.sourceforge.net/project/freetype/freetype2/2.4.10/freetype-2.4.10.tar.bz2 && tar -xf freetype-2.4.10.tar.bz2 && cd freetype-2.4.10 && ./configure --prefix=/opt/freetype && make -j4 && make install

# mysql client
RUN cd /tmp/build && wget "http://www.mysql.com/get/Downloads/MySQL-5.1/mysql-5.1.71.tar.gz/from/http://cdn.mysql.com/" -O mysql-5.1.71.tar.gz && tar -xf mysql-5.1.71.tar.gz && cd mysql-5.1.71 && ./configure --prefix=/opt/mysql51 --without-server --with-charset=utf8 && make -j4 && make install

# php5.2
RUN cd /tmp/build && wget http://museum.php.net/php5/php-5.2.17.tar.gz && tar -xf php-5.2.17.tar.gz 
# apply patches
RUN cd /tmp/build && patch -p1 -d php-5.2.17/ <patches/php-5.2.17-fpm-arm-0.5.14.diff && patch -p0 <patches/libxml29_compat.patch && patch -p0 <patches/suhosin-patch-5.2.17-0.9.7.patch
# build; Strange hack with echo avoids "Text file busy" error 
RUN cd /tmp/build/php-5.2.17 && echo " " >> /tmp/build/php-5.2.17/libevent/configure && ./configure --prefix=/opt/php52 --enable-fastcgi --enable-fpm --with-config-file-path=/opt/php52/conf/ --with-zlib --with-zlib-dir=/opt/zlib/ --with-bz2 --with-jpeg-dir=/opt/libjpeg --with-png-dir=/opt/libpng --with-gd --with-freetype-dir=/opt/freetype --with-curl=/opt/curl --with-openssl=/opt/openssl --with-gettext=/opt/gettext --with-curlwrapper --with-sqlite --enable-cli --enable-gd-native-ttf --enable-ftp --enable-mbstring --enable-sqlite-utf8 --enable-xml --enable-shmop  --enable-sysvsem --enable-inline-optimization --enable-mbregex --enable-force-cgi-redirect --enable-sockets --with-mysql=/opt/mysql51 --with-pdo-mysql=/opt/mysql51 --with-pgsql --with-pdo-pgsql && make -j4 && make install

# create php-fpm pool user/group
RUN groupadd www && useradd -g www www

# PECL extensions
RUN printf "\n" | /opt/php52/bin/pecl install memcache apc zip timezonedb geoip redis oauth xdebug-2.0.5 mongo-1.5.8

# conf
RUN rm -rf /tmp/*
ADD php.ini /opt/php52/conf/php.ini 
ADD php-fpm.conf /opt/php52/conf/php-fpm.conf


WORKDIR /opt

CMD ["/opt/php52/bin/php-cgi", "--fpm", "--fpm-config", "/opt/php52/conf/php-fpm.conf"]

# php fast-cgi server with fpm enabled
EXPOSE 9000

