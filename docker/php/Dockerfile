FROM php:fpm-alpine

LABEL authors="saydumgamzabekovich"

# Copy php config
COPY ./conf/php.ini /usr/local/etc/php/php.ini

# Packages
RUN apk add --no-cache curl git build-base zlib-dev oniguruma-dev autoconf icu-dev bash

# Postgres
RUN apk add --no-cache libpq-dev \
    && docker-php-ext-install pdo_pgsql

# Intl
RUN docker-php-ext-configure intl \
    && docker-php-ext-install intl

# OPCache (if you need it by any means)
# RUN docker-php-ext-install opcache

# XDebug
RUN yes | pecl install ${XDEBUG_VERSION} \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini


# Xdebug config
COPY ./conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Non root user
ARG PUID=1000
ARG PGID=1000
RUN apk --no-cache add shadow && \
    groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Git config
RUN git config --global user.email "saydum.it5@gmail.com" \
    && git config --global user.name "Saydum"

# Set user
USER www-data

# Files
COPY ./ /var/www
WORKDIR /var/www

# Run php-fpm
CMD php-fpm

# Expose port
EXPOSE 9000