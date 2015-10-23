FROM armbuild/ubuntu:15.04

RUN mkdir -p /tmp/
RUN mkdir -p /var/log/mysql51/
RUN mkdir -p /var/mysql51/data/

RUN groupadd -r mysql && useradd -r -g mysql mysql

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
	nano \
        wget curl \
	libbz2-dev \
	libncurses5-dev \
	autoconf \
	build-essential \
	libxml2-dev \ 
        zlibc zlib1g-dev

# mysql server
RUN cd /tmp/ && wget "http://www.mysql.com/get/Downloads/MySQL-5.1/mysql-5.1.71.tar.gz/from/http://cdn.mysql.com/" -O mysql-5.1.71.tar.gz && tar -xf mysql-5.1.71.tar.gz && cd mysql-5.1.71 && ./configure --prefix=/opt/mysql51 --with-charset=utf8 --with-mysqld-user=mysql --with-charset=utf8 --with-server-suffix="-innodb-plugin" --with-plugins=partition,archive,blackhole,csv,federated,heap,innobase,myisam && make && make install
RUN locale-gen en_US.UTF-8 && update-locale

ENV PATH $PATH:/opt/mysql51/libexec/:/opt/mysql51/bin/
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

VOLUME /var/mysql51/data/
ADD my.cnf /opt/mysql51/my.cnf 
ADD docker-entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

# default mysqld port
EXPOSE 3306

CMD ["mysqld", "--defaults-file=/opt/mysql51/my.cnf"]

