# Dockerfile
FROM php:8.2-apache

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Install necessary PHP extensions and Xdebug
RUN set -eux; \
    apt-get update; \
    apt-get upgrade -y; \
    apt-get install -y --no-install-recommends \
            curl \
            libmemcached-dev \
            libz-dev \
            libpq-dev \
            libjpeg-dev \
            libpng-dev \
            libfreetype6-dev \
            libssl-dev \
            libwebp-dev \
            libxpm-dev \
            libmcrypt-dev \
            libonig-dev; \
    rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    # Install the PHP pdo_mysql extention
    docker-php-ext-install pdo_mysql; \
    # Install the PHP pdo_pgsql extention
    docker-php-ext-install pdo_pgsql; \
    # Install the PHP gd library
    docker-php-ext-configure gd \
            --prefix=/usr \
            --with-jpeg \
            --with-webp \
            --with-xpm \
            --with-freetype; \
    docker-php-ext-install gd; \
    php -r 'var_dump(gd_info());'

# Install PHP MYSQL extension and restart server
RUN apt-get clean \
    && apt-get update \
    && docker-php-ext-install mysqli \
    && docker-php-ext-enable mysqli;
RUN service apache2 restart;

RUN apt-get update && apt-get install -y --no-install-recommends \
    autoconf \
    dpkg-dev \
    file \
    g++ \
    gcc \
    make \
    pkg-config \
    re2c \
    && rm -rf /var/lib/apt/lists/* \
    && pecl install xdebug-3.4.0 \
        && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.mode=develop,debug" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.idekey=PHPSTORM" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.discover_client_host=0" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini \
        && docker-php-ext-enable xdebug

# Copy Apache VirtualHost configuration
COPY docker/apache/kuflinkcom.conf /etc/apache2/sites-available/kuflinkcom.conf
RUN a2ensite kuflinkcom.conf
