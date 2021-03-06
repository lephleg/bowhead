#
# Bowhead Dockerfile.
# use from the bowhead dir as
# `docker built -t bowhead bowhead/
# then `docker run bowhead` and read the output at the end.
# using `docker ps` and `docker exec -it {id} /bin/bash` to connect
#

FROM thatsamguy/trusty-php71

MAINTAINER joeldg@gmail.com

RUN \
  apt-get -y update && \
  apt-get -y install \
  curl vim wget git build-essential make gcc nasm mlocate \
  software-properties-common python-software-properties \
  nginx supervisor unattended-upgrades nfs-common jq python-pip cron libmcrypt-dev php7.1-fpm

RUN apt-get install -y pkg-config php-dompdf php-pear php-imagick autoconf automake libtool m4 \
    screen autoconf automake libtool m4 pkg-config \
    unzip python-pip supervisor

RUN pecl channel-update pecl.php.net
RUN pecl install trader

#install phpUnit & composer and add a healthcheck we can use in AWS
RUN \
    wget "https://phar.phpunit.de/phpunit.phar" && \
    chmod +x phpunit.phar && \
    mv phpunit.phar /usr/local/bin/phpunit && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    dpkg-reconfigure --priority=low unattended-upgrades && \
    echo "<?php phpinfo() ?>" > /var/www/html/healthcheck.php

WORKDIR /var/www/bowhead

ADD resources/etc/ /etc/
ADD resources/config_bowhead.sh /var/www/config_bowhead.sh

RUN chmod +x /var/www/config_bowhead.sh

# if this container is used in AWS you will need these for ECS
# you will also want the ports below
RUN \
    pip install awscli && \
    aws configure set preview.efs true

RUN usermod -u 1000 www-data

EXPOSE 80
EXPOSE 2049
EXPOSE 20048
#
# supervisord with nodaemon=true
# it sits and waits and keeps docker going.
#
CMD ["/var/www/config_bowhead.sh"]