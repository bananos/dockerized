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
 	postgresql-server-dev-9.4

# zlib
RUN cd /tmp/build && wget "http://zlib.net/zlib-1.2.8.tar.gz" && tar -xf zlib-1.2.8.tar.gz && cd zlib-1.2.8 && ./configure --prefix=/opt/zlib && make && make install

# libjpeg
RUN cd /tmp/build && wget "http://www.ijg.org/files/jpegsrc.v8d.tar.gz" && tar -xf jpegsrc.v8d.tar.gz && cd jpeg-8d && ./configure --prefix=/opt/libjpeg && make && make install

# libpng
RUN cd /tmp/build && wget "http://downloads.sourceforge.net/project/libpng/libpng16/1.6.18/libpng-1.6.18.tar.gz" && tar -xf libpng-1.6.18.tar.gz && cd libpng-1.6.18 && ./configure --prefix=/opt/libpng && make && make install

# CURL
RUN cd /tmp/build && wget "http://curl.haxx.se/download/curl-7.30.0.tar.gz" && tar -xf curl-7.30.0.tar.gz && cd curl-7.30.0 && ./configure --prefix=/opt/curl && make && make install

# openssl 
RUN cd /tmp/build && curl "https://www.openssl.org/source/old/0.9.x/openssl-0.9.8zf.tar.gz" > openssl-0.9.8zf.tar.gz && tar -xf openssl-0.9.8zf.tar.gz && cd openssl-0.9.8zf && ./config --prefix=/opt/openssl && make && make install

# gettext
RUN cd /tmp/build && wget ftp://ftp.gnu.org/gnu/gettext/gettext-0.19.6.tar.gz && tar -xf gettext-0.19.6.tar.gz && cd gettext-0.19.6 && ./configure --prefix=$PREFIX/gettext --disable-dependency-tracking --disable-debug --without-included-gettext --without-included-glib --without-included-libcroco --without-included-libxml --without-emacs --without-git --without-cvs && make && make install

# freetype
RUN cd /tmp/build && wget http://downloads.sourceforge.net/project/freetype/freetype2/2.4.10/freetype-2.4.10.tar.bz2 && tar -xf freetype-2.4.10.tar.bz2 && cd freetype-2.4.10 && ./configure --prefix=/opt/freetype && make && make install

# mysql client
RUN cd /tmp/build && wget "http://www.mysql.com/get/Downloads/MySQL-5.1/mysql-5.1.71.tar.gz/from/http://cdn.mysql.com/" -O mysql-5.1.71.tar.gz && tar -xf mysql-5.1.71.tar.gz && cd mysql-5.1.71 && ./configure --prefix=/opt/mysql51 --without-server --with-charset=utf8 && make && make install

# php5.2
RUN cd /tmp/build && wget http://museum.php.net/php5/php-5.2.17.tar.gz && tar -xf php-5.2.17.tar.gz 
# apply patches
RUN cd /tmp/build && patch -p0 <patches/libxml29_compat.patch && patch -p0 <patches/suhosin-patch-5.2.17-0.9.7.patch
# build
RUN cd /tmp/build/php-5.2.17 && ./configure --prefix=/opt/php52 --enable-fastcgi --with-zlib --with-zlib-dir=/opt/zlib/ --with-bz2 --with-jpeg-dir=/opt/libjpeg --with-png-dir=/opt/libpng --with-gd --with-freetype-dir=/opt/freetype --with-curl=/opt/curl --with-openssl=/opt/openssl --with-gettext=/opt/gettext --with-curlwrapper --with-sqlite --enable-cli --enable-gd-native-ttf --enable-ftp --enable-mbstring --enable-sqlite-utf8 --enable-xml --enable-shmop  --enable-sysvsem --enable-inline-optimization --enable-mbregex --enable-force-cgi-redirect --enable-sockets --with-mysql=/opt/mysql51 --with-pdo-mysql=/opt/mysql51 --with-pgsql --with-pdo-pgsql && make && make install

# eAccelerator
RUN cd /tmp/build && wget "https://github.com/downloads/eaccelerator/eaccelerator/eaccelerator-0.9.6.1.tar.bz2" && tar -xf eaccelerator-0.9.6.1.tar.bz2 
RUN cd /tmp/build/eaccelerator-0.9.6.1 && /opt/php52/bin/phpize 
RUN cd /tmp/build/eaccelerator-0.9.6.1 || autoconf || automake 
RUN cd /tmp/build/eaccelerator-0.9.6.1 && ./configure --prefix=/opt/eaccelerator --enable-eaccelerator=shared --with-php-config=/opt/php52/bin/php-config  --with-eaccelerator-userid=1
RUN cd /tmp/build/eaccelerator-0.9.6.1 && make && make install

# PECL extensions
RUN printf "\n" | /opt/php52/bin/pecl install memcache apc zip timezonedb geoip redis oauth xdebug-2.0.5 mongo-1.5.8

# conf
RUN mkdir -p /opt/var/eaccelerator_cache
RUN rm -rf /tmp/*
ADD php.ini /opt/php52/lib/php.ini 

WORKDIR /opt

CMD ["/opt/php52/bin/php-cgi", "-b", "0.0.0.0:9000"]

# php fast-cgi server
EXPOSE 9000

