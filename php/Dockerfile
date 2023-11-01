ARG PHP_VERSION

FROM php:${PHP_VERSION}-fpm-alpine

ARG TZ
# https://wiki.alpinelinux.org/wiki/Setting_the_timezone
RUN echo "${TZ}" && apk --update add tzdata && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    apk del tzdata

RUN apk add --update linux-headers

RUN apk add --no-cache bash mysql-client msmtp perl wget procps shadow libzip libpng libjpeg-turbo libwebp freetype icu \
    libintl patch acl git gettext-dev libcurl libxml2-dev openssh-client pcre-dev su-exec build-base oniguruma-dev \
    gmp-dev postgresql-dev gettext gettext-dev htop supervisor nodejs npm yarn vim

RUN apk add --update --no-cache --virtual build-essentials \
    icu-dev icu-libs zlib-dev g++ make automake autoconf libzip-dev \
    libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg --with-webp && \
    docker-php-ext-configure gettext && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure pcntl --enable-pcntl && \
    docker-php-ext-configure soap && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl && \
    docker-php-ext-install gettext && \
    docker-php-ext-install pcntl && \
    docker-php-ext-install gd && \
    docker-php-ext-install opcache && \
    docker-php-ext-install soap && \
    docker-php-ext-install xml && \
    docker-php-ext-install gmp && \
    docker-php-ext-install pdo_pgsql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install opcache && \
    docker-php-ext-install exif && \
    docker-php-ext-install zip && \
    docker-php-ext-install bcmath && \
    apk del build-essentials && rm -rf /usr/src/php*

RUN apk add --no-cache $PHPIZE_DEPS

ARG PHP_XDEBUG
RUN if [ "$PHP_XDEBUG" = "on" ]; then \
    yes | pecl install -o -f xdebug  && \
    rm -rf /tmp/pear && \
    docker-php-ext-enable xdebug && \
    echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.mode=develop,debug" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.discover_client_host = 0 " >> /usr/local/etc/php/conf.d/xdebug.ini \
else \
    echo "Skip xdebug support"; \
fi

# Clean
RUN rm -rf /var/cache/apk/* && docker-php-source delete

USER root

RUN curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.alpine.sh' | bash
RUN apk add --no-cache symfony-cli

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer
RUN /usr/local/bin/composer self-update

USER www-data:www-data

WORKDIR /var/www/