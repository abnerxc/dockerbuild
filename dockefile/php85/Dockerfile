FROM php:8.5.12-fpm-alpine

ENV PECL_MIRROR_URL=https://mirrors.aliyun.com/pecl

# 1. 更新apk源 + 安装系统依赖；telnet替换busybox-extras
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache linux-headers g++ make librdkafka-dev libmemcached-dev freetype-dev libjpeg-turbo-dev libpng-dev curl icu-dev libxml2-dev libxslt-dev bzip2-dev gettext-dev openssl autoconf pkgconfig libssh-dev ffmpeg libwebp-dev yasm ffmpeg-dev libzip-dev oniguruma-dev rabbitmq-c rabbitmq-c-dev busybox-extras

# gd 单独编译
RUN docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install gd

# 分组编译原生扩展，【opcache移除，PHP8.5内核自带】
RUN docker-php-ext-install intl soap xsl bz2 zip

RUN docker-php-ext-install pcntl pdo_mysql mysqli mbstring exif bcmath

RUN docker-php-ext-install calendar sockets gettext shmop

RUN docker-php-ext-install sysvmsg sysvsem sysvshm

# 3. 初始化pecl，安装编译依赖包(PHPIZE_DEPS)
RUN pecl channel-update pecl.php.net \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS

# ========== 逐个PECL扩展，每条RUN，失败即停 ==========
# xdebug 3.5.1 支持PHP8.5
RUN pecl install -o -f xdebug-3.5.1 && docker-php-ext-enable xdebug

# redis升级7.3.0，修复php8.5头文件缺失
RUN pecl install -o -f redis-7.3.0 && docker-php-ext-enable redis

RUN curl -fsSL https://github.com/php-memcached-dev/php-memcached/archive/refs/heads/main.tar.gz -o memcached.tar.gz \
    && mkdir -p /tmp/memcached && tar -xf memcached.tar.gz -C /tmp/memcached --strip-components=1 \
    && docker-php-ext-configure /tmp/memcached \
    && docker-php-ext-install /tmp/memcached

RUN pecl install -o -f mongodb-2.5.5 && docker-php-ext-enable mongodb

RUN pecl install -o -f swoole-6.2.3 && docker-php-ext-enable swoole

RUN pecl install -o -f protobuf-5.36.2 && docker-php-ext-enable protobuf

RUN pecl install -o -f amqp-2.2.0 && docker-php-ext-enable amqp

RUN pecl install -o -f rdkafka-6.0.5 && docker-php-ext-enable rdkafka

# ========== 外部源码编译 tideways xhprof ==========
RUN pecl install -o -f xhprof-2.3.10 && docker-php-ext-enable xhprof

# ========== 安装phar工具 composer / php‑cs‑fixer / phpunit ==========
RUN curl -fsSL --connect-timeout 60 https://github.com/composer/composer/releases/download/2.10.3/composer.phar -o /usr/local/bin/composer \
    && chmod u+x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

RUN curl -fsSL --connect-timeout 60 https://github.com/PHP-CS-Fixer/PHP-CS-Fixer/releases/download/v3.95.27/php-cs-fixer.phar -o /usr/local/bin/php-cs-fixer \
    && chmod u+x /usr/local/bin/php-cs-fixer

RUN curl -fsSL --connect-timeout 60 https://phar.phpunit.de/phpunit-13.4.3.phar -o /usr/local/bin/phpunit \
    && chmod u+x /usr/local/bin/phpunit

# 调试阶段不删除编译依赖，方便看报错；定位完成后生产镜像再打开apk del
RUN docker-php-source delete \
    && rm -rf /tmp/* /var/lib/apt/lists/* /tmp/pear ~/.pearrc /usr/share/php
# RUN apk del .phpize-deps
