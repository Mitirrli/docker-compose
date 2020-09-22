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

RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
  && chmod a+x /usr/local/bin/composer \
  && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
  && composer self-update --clean-backups

WORKDIR /var/www/html

ENTRYPOINT ["php-fpm", "-R"]

