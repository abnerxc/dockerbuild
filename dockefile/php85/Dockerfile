FROM php:8.5.12-fpm-alpine

# 设置pecl国内镜像，解决国外源超时
ENV PECL_MIRROR_URL=https://mirrors.aliyun.com/pecl

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && apk update \
    && apk add --no-cache linux-headers g++ make librdkafka-dev libmemcached-dev freetype-dev libjpeg-turbo-dev libpng-dev curl icu-dev libxml2-dev libxslt-dev bzip2-dev gettext-dev openssl autoconf pkgconfig libssh-dev ffmpeg libwebp-dev yasm ffmpeg-dev libzip-dev oniguruma-dev rabbitmq-c rabbitmq-c-dev telnet \
    && docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install gd intl soap xsl bz2 zip pcntl pdo_mysql mysqli mbstring exif bcmath calendar sockets gettext shmop sysvmsg sysvsem sysvshm opcache \
    && pecl channel-update pecl.php.net \
    && set -xe && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    # xmlrpc:废弃不支持php8.5
    # && pecl install -o -f xmlrpc-1.0.0RC3 \
    # && docker-php-ext-enable xmlrpc \
    && pecl install -o -f xdebug-3.4.2 \
    && docker-php-ext-enable xdebug \
    && pecl install -o -f redis-6.2.0 \
    && docker-php-ext-enable redis \
    && pecl install -o -f memcached-3.3.0 \
    && docker-php-ext-enable memcached \
    && pecl install -o -f mongodb-1.20.0 \
    && docker-php-ext-enable mongodb \
    && pecl install -o -f swoole-5.1.6 \
    && docker-php-ext-enable swoole \
    # protobuf 4.x编译内存消耗巨大，云构建降级3.25.5兼容php8.5
    && pecl install -o -f protobuf-3.25.5 \
    && docker-php-ext-enable protobuf \
    # amqp‑2.0.0对系统rabbitmq‑c版本要求过高，alpine3.24用1.12.0
    && pecl install -o -f amqp-1.12.0 \
    && docker-php-ext-enable amqp \
    && pecl install -o -f rdkafka-6.0.3 \
    && docker-php-ext-enable rdkafka \
    # SeasClick不再维护，php8+不可编译
    # && pecl install -o -f SeasClick-0.1.1 \
    # && docker-php-ext-enable SeasClick \
    && curl -fsSL --connect-timeout 60 'https://github.com/tideways/php-xhprof-extension/archive/refs/tags/v5.0.4.tar.gz' -o tideways.tar.gz  \
    && mkdir -p /tmp/tideways  \
    && tar -xf tideways.tar.gz -C /tmp/tideways --strip-components=1  \
    && docker-php-ext-configure /tmp/tideways  \
    && docker-php-ext-install /tmp/tideways \
    # composer
    && curl -fsSL --connect-timeout 60 https://github.com/composer/composer/releases/download/2.8.6/composer.phar -o /usr/local/bin/composer \
    && chmod u+x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    # php‑cs‑fixer
    && curl -fsSL --connect-timeout 60 https://github.com/FriendsOfPHP/PHP-CS-Fixer/releases/download/v3.65.0/php-cs-fixer.phar -o /usr/local/bin/php-cs-fixer \
    && chmod u+x /usr/local/bin/php-cs-fixer \
    # phpunit11
    && curl -fsSL --connect-timeout 60 https://phar.phpunit.de/phpunit-11.phar -o /usr/local/bin/phpunit \
    && chmod u+x /usr/local/bin/phpunit \
    && docker-php-source delete \
    && rm -rf /tmp/* && rm -rf /var/lib/apt/lists/* && rm -rf /tmp/pear ~/.pearrc && rm -rf /usr/share/php \
    && apk del .phpize-deps
