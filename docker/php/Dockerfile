FROM php:7-fpm-alpine

MAINTAINER vasmovzh <vasmovzh@yandex.ru>

# Installing required packages
RUN apk add --update --no-cache \
    autoconf \
    bash \
    curl \
    docker \
    docker-compose \
    g++ \
    gcc \
    git \
    graphviz-dev \
    icu-dev \
    libpng-dev \
    libxslt-dev \
    libzip-dev \
    make \
    postgresql-dev \
    rabbitmq-c-dev \
    yarn \
    zlib-dev

# Installing simple required extensions
RUN docker-php-ext-install gd intl pdo_pgsql xsl zip

# Installing complex required extensions
RUN yes "" | pecl install -a amqp redis xdebug
RUN docker-php-ext-enable amqp redis xdebug

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

# Installing symfony
RUN curl -sS https://get.symfony.com/cli/installer | bash
RUN mv /root/.symfony/bin/symfony /usr/local/bin/symfony
