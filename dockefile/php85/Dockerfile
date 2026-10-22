FROM php:8.5.12-fpm-alpine


# 1. 更新apk源 + 安装系统依赖；telnet替换busybox-extras
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache linux-headers g++ make librdkafka-dev libmemcached-dev freetype-dev libjpeg-turbo-dev libpng-dev curl icu-dev libxml2-dev libxslt-dev bzip2-dev gettext-dev openssl autoconf pkgconfig libssh-dev ffmpeg libwebp-dev yasm ffmpeg-dev libzip-dev oniguruma-dev rabbitmq-c rabbitmq-c-dev busybox-extras

# gd 单独编译
RUN docker-php-ext-configure gd --with-webp=/usr/include/webp --with-jpeg=/usr/include --with-freetype=/usr/include/freetype2/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install intl soap xsl bz2 zip \
    && docker-php-ext-install pcntl pdo_mysql mysqli mbstring exif bcmath \
    && docker-php-ext-install calendar sockets gettext shmop \
    && docker-php-ext-install sysvmsg sysvsem sysvshm \
    && pecl channel-update pecl.php.net \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && pecl install -o -f xdebug-3.5.1 && docker-php-ext-enable xdebug \
    && pecl install -o -f redis-7.3.0 && docker-php-ext-enable redis \
    && pecl install -o -f memcached-3.4.0 && docker-php-ext-enable memcached \
    && pecl install -o -f mongodb-2.5.5 && docker-php-ext-enable mongodb \
    && pecl install -o -f swoole-6.2.3 && docker-php-ext-enable swoole \
    && pecl install -o -f protobuf-5.36.2 && docker-php-ext-enable protobuf \
    && pecl install -o -f amqp-2.2.0 && docker-php-ext-enable amqp \
    && pecl install -o -f rdkafka-6.0.5 && docker-php-ext-enable rdkafka \
    && pecl install -o -f xhprof-2.3.10 && docker-php-ext-enable xhprof \
    && curl -fsSL --connect-timeout 60 https://github.com/composer/composer/releases/download/2.10.3/composer.phar -o /usr/local/bin/composer \
    && chmod u+x /usr/local/bin/composer \
    && composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ \
    && curl -fsSL --connect-timeout 60 https://github.com/PHP-CS-Fixer/PHP-CS-Fixer/releases/download/v3.95.27/php-cs-fixer.phar -o /usr/local/bin/php-cs-fixer \
    && chmod u+x /usr/local/bin/php-cs-fixer \
    && curl -fsSL --connect-timeout 60 https://phar.phpunit.de/phpunit-13.4.3.phar -o /usr/local/bin/phpunit \
    && chmod u+x /usr/local/bin/phpunit \ 
    && docker-php-source delete \
    && rm -rf /tmp/* /var/lib/apt/lists/* /tmp/pear ~/.pearrc /usr/share/php \
    && apk del .phpize-deps
