FROM php:8.5.12-fpm-alpine

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && apk update \
    && apk add --no-cache linux-headers g++ make librdkafka-dev libmemcached-dev freetype-dev libjpeg-turbo-dev libpng-dev curl icu-dev libxml2-dev libxslt-dev bzip2-dev gettext-dev openssl autoconf pkgconfig libssh-dev ffmpeg libwebp-dev yasm  ffmpeg-dev libzip-dev oniguruma-dev rabbitmq-c rabbitmq-c-dev telnet \
    && docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install gd intl soap xsl  bz2 zip pcntl pdo_mysql mysqli mbstring exif bcmath calendar sockets gettext shmop sysvmsg sysvsem sysvshm opcache \
    && pecl channel-update pecl.php.net \
    && set -xe && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    # xmlrpc: 1.0.0RC3 废弃RC版本，不支持PHP8.5，已经从pecl废弃，如需该功能建议使用php库实现，注释关闭编译
    # && pecl install -o -f xmlrpc-1.0.0RC3 \
    # && docker-php-ext-enable xmlrpc \
    && pecl install -o -f xdebug-3.4.2 \
    && docker-php-ext-enable xdebug \
    && pecl install -o -f redis-6.2.0 \
    && docker-php-ext-enable redis \
    && pecl install -o -f memcached-3.3.0 \
    && docker-php-ext-enable memcached \
    # mongodb:原版本1.51.1不存在，修正官方稳定版本
    && pecl install -o -f mongodb-1.20.0 \
    && docker-php-ext-enable mongodb \
    # swoole升级支持PHP8.5
    && pecl install -o -f swoole-5.1.6 \
    && docker-php-ext-enable swoole \
    && pecl install -o -f protobuf-4.28.3 \
    && docker-php-ext-enable protobuf \
    && pecl install -o -f amqp-2.0.0 \
    && docker-php-ext-enable amqp \
    && pecl install -o -f rdkafka-6.0.3 \
    && docker-php-ext-enable rdkafka \
    # SeasClick-0.1.1：停止维护，不支持PHP8+，编译失败，注释。业务必须使用该扩展则PHP版本最高7.4
    # && pecl install -o -f SeasClick-0.1.1 \
    # && docker-php-ext-enable SeasClick \
    && curl -fsSL 'https://github.com/tideways/php-xhprof-extension/archive/refs/tags/v5.0.4.tar.gz' -o tideways.tar.gz  \
    && mkdir -p /tmp/tideways  \
    && tar -xf tideways.tar.gz -C /tmp/tideways --strip-components=1  \
    && docker-php-ext-configure /tmp/tideways  \
    && docker-php-ext-install /tmp/tideways \
    # 更新composer到2.8.x稳定版适配php8.5，配置阿里云镜像
    && wget -nv -O /usr/local/bin/composer https://github.com/composer/composer/releases/download/2.8.6/composer.phar \
    && chmod u+x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # php‑cs‑fixer v2已经停止维护，升级v3
    && wget -nv -O /usr/local/bin/php-cs-fixer https://github.com/FriendsOfPHP/PHP-CS-Fixer/releases/download/v3.65.0/php-cs-fixer.phar \
    && chmod u+x /usr/local/bin/php-cs-fixer \
    # phpunit，php8.5使用phpunit11
    && wget -nv -O /usr/local/bin/phpunit https://phar.phpunit.de/phpunit-11.phar \
    && chmod u+x /usr/local/bin/phpunit \
    && docker-php-source delete \
    && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/pear ~/.pearrc && rm -rf /usr/share/php \
    && apk del  .phpize-deps
