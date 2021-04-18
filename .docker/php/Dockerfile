# --------------------------------------------------------------------------------
#                               Local Dockerfile
# --------------------------------------------------------------------------------
# @build-example docker-compose up -d
# --------------------------------------------------------------------------------
ARG PHP_VERSION
FROM php:${PHP_VERSION}

# 使用国内镜像
RUN echo http://mirrors.aliyun.com/alpine/v3.12/main > /etc/apk/repositories \
    && echo  http://mirrors.aliyun.com/alpine/v3.12/community >> /etc/apk/repositorie

RUN apk add --no-cache oniguruma-dev \
    curl-dev \
    libxml2-dev \
    libpng-dev freetype \
    libpng \
    libjpeg-turbo \
    freetype-dev \
    libpng-dev \
    jpeg-dev \
    libjpeg \
    libjpeg-turbo-dev \
    icu-dev \
    gcc \
    g++ \
    make \
    autoconf

# 安装GD库以及其他扩展
RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install gd bcmath pdo_mysql mysqli opcache intl pcntl sockets

# 安装composer
RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
  && chmod a+x /usr/local/bin/composer \
  && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
  && composer self-update --clean-backups

# 安装redis
ARG PHPREDIS_VERSION
ARG INSTALL_REDIS
RUN if [ ${INSTALL_REDIS} = true ]; then \
  wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
  && pecl install /tmp/redis.tar.tgz \
  && rm -rf /tmp/redis.tar.tgz \
  && docker-php-ext-enable redis \
;fi

# 安装xdebug
ENV PHPXDEBUG_VERSION=3.0.4
RUN wget http://pecl.php.net/get/xdebug-${PHPXDEBUG_VERSION}.tgz -O /tmp/xdebug.tar.tgz \
    && pecl install /tmp/xdebug.tar.tgz \
    && rm -rf /tmp/xdebug.tar.tgz \ 
    && docker-php-ext-enable xdebug \
    && echo 'xdebug.mode = debug' > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo 'xdebug.start_with_request = yes' >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo 'xdebug.client_host = 192.168.0.132' >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo 'xdebug.log_level = 0' >> /usr/local/etc/php/conf.d/xdebug.ini

WORKDIR /www

EXPOSE 9005

CMD php -S 0.0.0.0:9005

