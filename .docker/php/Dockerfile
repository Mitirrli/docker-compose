# ------------------------------------------------------------------------------------
#                                  Local Dockerfile
# ------------------------------------------------------------------------------------
# @build-example docker-compose up -d
# ------------------------------------------------------------------------------------
ARG PHP_VERSION
ARG PHPREDIS_VERSION
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

# Install Some Extensions.
RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install gd bcmath pdo_mysql mysqli opcache pcntl mbstring sockets

# Install Composer.
RUN wget https://mirrors.aliyun.com/composer/composer.phar -O /usr/local/bin/composer \
    && chmod a+x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer \
    && composer self-update --clean-backups \
    # Install Redis Extension.
    && wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
    && pecl install /tmp/redis.tar.tgz \
    && rm -rf /tmp/redis.tar.tgz \
    && docker-php-ext-enable redis

# Install Xdebug Extension
RUN pecl install xdebug && docker-php-ext-enable xdebug
RUN { \
        echo 'xdebug.remote_enable = 1'; \
        echo 'xdebug.remote_autostart = 1'; \
        echo 'xdebug.remote_port = 9001'; \
        echo 'xdebug.remote_host = docker.for.mac.localhost'; \
    } > /usr/local/etc/php/conf.d/xdebug.ini

WORKDIR /var/www/html

ENTRYPOINT ["php-fpm", "-R"]

