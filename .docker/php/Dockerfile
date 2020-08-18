# ------------------------------------------------------------------------------------
#                                  Local Dockerfile
# ------------------------------------------------------------------------------------
# @build-example docker-compose up -d
# ------------------------------------------------------------------------------------
ARG PHP_VERSION
FROM php:${PHP_VERSION}

RUN echo http://mirrors.aliyun.com/alpine/v3.12/main > /etc/apk/repositories && echo http://mirrors.aliyun.com/alpine/v3.12/community >> /etc/apk/repositories

RUN apk add --no-cache oniguruma-dev \
  curl-dev \
  libxml2-dev \
  libzip-dev \
  libpng-dev freetype \
  libpng \
  openssl-dev \
  libffi-dev \
  libjpeg-turbo \
  freetype-dev \
  libpng-dev \
  jpeg-dev \
  libjpeg \
  libjpeg-turbo-dev \
  gcc \
  g++ \
  make \
  autoconf \
  icu-dev

RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
  && docker-php-ext-install gd bcmath pdo_mysql mysqli opcache pcntl mbstring sockets

RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
  && chmod a+x /usr/local/bin/composer \
  && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
  && composer self-update --clean-backups

ARG PHPREDIS_VERSION
ARG INSTALL_REDIS=false
RUN if [ ${INSTALL_REDIS} = true ]; then \
  wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
  && pecl install /tmp/redis.tar.tgz \
  && rm -rf /tmp/redis.tar.tgz \
  && docker-php-ext-enable redis \
;fi

ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
  pecl install xdebug \
  && docker-php-ext-enable xdebug \
  && echo 'xdebug.remote_enable = 1' > /usphp -r/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_autostart = 1' >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_port = 9001' >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo 'xdebug.remote_host = docker.for.mac.localhost' >> /usr/local/etc/php/conf.d/xdebug.ini \
;fi

ARG SWOOLE_VERSION
ARG INSTALL_SWOOLE=false
RUN if [ ${INSTALL_SWOOLE} = true ]; then \
  wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O swoole.tar.gz \
  && mkdir -p swoole \
  && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
  && rm swoole.tar.gz \
  && ( \
    cd swoole \
      && phpize \
      && ./configure --enable-mysqlnd --enable-sockets --enable-openssl --enable-http2 \
      && make -j$(nproc) \
      && make install \
    ) \
  && rm -r swoole \
  && docker-php-ext-enable swoole \
  && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/swoole.ini \
;fi

WORKDIR /var/www/html

ENTRYPOINT ["php-fpm", "-R"]

