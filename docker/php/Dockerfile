FROM php:7.3-fpm-alpine as builder

MAINTAINER vasmovzh <vasmovzh@yandex.ru>

RUN apk add --update --no-cache $PHPIZE_DEPS postgresql-dev autoconf git make curl wget libmcrypt-dev make shadow \
    icu-dev libc-dev libxml2-dev libxslt-dev zip unzip openssh gcc g++ libc-dev \
    freetype-dev libpng-dev libjpeg-turbo-dev  imagemagick imagemagick-dev

# Installing required extensions
RUN yes "" | pecl install xdebug apcu && \
    docker-php-ext-enable xdebug apcu && \
    docker-php-ext-install mbstring opcache intl soap json hash ctype sockets pdo ftp xsl dba tokenizer pgsql pdo_pgsql && \
    docker-php-ext-install xmlrpc xmlwriter

# Installing composer
RUN EXPECTED_SIGNATURE="$(wget -q -O - https://composer.github.io/installer.sig)" && \
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    ACTUAL_SIGNATURE="$(php -r "echo hash_file('sha384', 'composer-setup.php');")" && \
    if [ "$EXPECTED_SIGNATURE" != "$ACTUAL_SIGNATURE" ] ; then \
        >&2 echo 'ERROR: Invalid installer signature' ; \
        rm composer-setup.php ; \
        exit 1 ; \
    fi ; \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer ; \
    composer --version ; \
    rm composer-setup.php


# Preparing directories for exportin for the next building stages
RUN mkdir -p /app/prod/ext /app/prod/conf.d /app/dev/ext/$(basename $(php-config --extension-dir)) /app/dev/conf.d
RUN echo "extension=apcu.so" > /usr/local/etc/php/conf.d/apcu.ini

# Moving files for development-build, so these files would nit get into production-build
RUN mv /usr/local/lib/php/extensions/$(basename $(php-config --extension-dir))/xdebug.so \
       /app/dev/ext/$(basename $(php-config --extension-dir))/ && \
    mv /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini /app/dev/conf.d/

# Copying required files and configs
RUN cp -r /usr/local/lib/php/extensions/* /app/prod/ext && \
    cp /usr/local/etc/php/conf.d/* /app/prod/conf.d/

FROM php:7.3-fpm-alpine

# For production
COPY --from=builder /app/prod/ext /usr/local/lib/php/extensions
COPY --from=builder /app/prod/conf.d/* /usr/local/etc/php/conf.d/

# For development
COPY --from=builder /app/dev/ext /usr/local/lib/php/extensions
COPY --from=builder /app/dev/conf.d/* /usr/local/etc/php/conf.d/
