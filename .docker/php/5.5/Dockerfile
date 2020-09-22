# ------------------------------------------------------------------------------------
#                                  Local Dockerfile
# ------------------------------------------------------------------------------------
# @build-example docker-compose up -d
# ------------------------------------------------------------------------------------
ARG PHP5_VERSION
FROM php:${PHP5_VERSION}

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

RUN apk update && apk add --no-cache openssl git libmcrypt-dev freetype-dev libjpeg-turbo-dev gcc g++ make autoconf

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/ \
  && docker-php-ext-install gd bcmath mcrypt pdo_mysql

ARG PHP5REDIS_VERSION
ARG INSTALL_REDIS=false
RUN if [ ${INSTALL_REDIS} = true ]; then \
  wget http://pecl.php.net/get/redis-${PHP5REDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
  && pecl install /tmp/redis.tar.tgz \
  && rm -rf /tmp/redis.tar.tgz \
  && docker-php-ext-enable redis \
;fi

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
  git clone https://gitee.com/mirrorgit/xdebug.git \
  && cd xdebug \
  && git checkout tags/XDEBUG_2_5_5 \
  && phpize \
  && ./configure --enable-xdebug \
  && make \
  && make install \
  && docker-php-ext-enable xdebug \
  && echo 'xdebug.remote_enable = 1' > /usr/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_autostart = 1' >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_port = 9001' >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_host = docker.for.mac.localhost' >> /usr/local/etc/php/conf.d/xdebug.ini \
;fi

RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
  && chmod a+x /usr/local/bin/composer \
  && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
  && composer self-update --clean-backups

WORKDIR /var/www/html

ENTRYPOINT ["php-fpm", "-R"]

