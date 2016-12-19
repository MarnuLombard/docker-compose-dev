FROM php:fpm
MAINTAINER Marnu Lombard <marnu@mar.nu>

ADD install_composer.sh /usr/bin/

RUN apt-get update && apt-get install -y \
        pkg-config \
        libssl-dev \
        libfreetype6-dev libpng12-dev libjpeg62-turbo-dev \
        libmcrypt-dev \
        libicu-dev \
        libxml2-dev

# OpenSSl
RUN docker-php-source extract \
    && cp /usr/src/php/ext/openssl/config0.m4 /usr/src/php/ext/openssl/config.m4 \
    && docker-php-ext-configure openssl --with-openssl=/usr --with-libdir=/lib/x86_64-linux-gnu \
    && docker-php-ext-install -j$(nproc) openssl \

# GD
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-enable gd \

# MCrypt
    && docker-php-ext-install mcrypt \
    && docker-php-ext-enable mcrypt \

# Intl
    && docker-php-ext-install intl \
    && docker-php-ext-enable intl \

# Mysql
    && docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-enable pdo pdo_mysql \

# mbstring
    && docker-php-ext-install mbstring \
    && docker-php-ext-enable mbstring \

# SOAP
    && docker-php-ext-install soap \
    && docker-php-ext-enable soap \

# Opcache
    && docker-php-ext-install opcache \
    && docker-php-ext-enable opcache \

# Install PHP zip extension
    && docker-php-ext-install zip \
    && docker-php-ext-enable zip \

# XDebug
    && pecl install -o -f xdebug \
    && docker-php-ext-enable xdebug \
    && rm -rf /tmp/pear \

# Composer
    && sh /usr/bin/install_composer.sh \
    && rm /usr/bin/install_composer.sh \

# Install Blackfire.io Probe
    && export VERSION=`php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;"` \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/${VERSION} \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so `php -r "echo ini_get('extension_dir');"`/blackfire.so \
    && echo "extension=blackfire.so\nblackfire.agent_socket=\${BLACKFIRE_PORT}" > $PHP_INI_DIR/conf.d/blackfire.ini \

# Sample index.php
    && echo "<?php phpinfo();" > /var/www/html/index.php \

# Clean
    && docker-php-source delete \
    && apt-get autoremove -qy \
    && apt-get autoclean -qy
