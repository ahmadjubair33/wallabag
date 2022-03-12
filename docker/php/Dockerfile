FROM php:8.0.16-fpm

RUN apt-get update && apt-get install -y \
        libmcrypt-dev \
        libicu-dev \
        libpq-dev \
        libxml2-dev \
        libpng-dev \
        libjpeg-dev \
        libsqlite3-dev \
        imagemagick \
        libmagickwand-dev \
        libtidy-dev \
        libonig-dev \
        libzip-dev \
        libfreetype6-dev \
        zlib1g-dev \
        git \
        build-essential
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install -j "$(nproc)" \
        bcmath \
        gd \
        gettext \
        iconv \
        intl \
        mbstring \
        opcache \
        pdo \
        pdo_mysql \
        pdo_pgsql \
        pdo_sqlite \
        sockets \
        tidy \
        zip

RUN pecl install redis; \
    pecl install imagick; \
    pecl install xdebug; \
    docker-php-ext-enable \
        redis \
        imagick \
        xdebug \
    ;

RUN curl -fsSL https://deb.nodesource.com/setup_14.x \
    | bash - ; \
    apt install -y nodejs ; \
    npm install -g yarn

RUN curl -L -o /usr/local/bin/envsubst https://github.com/a8m/envsubst/releases/download/v1.1.0/envsubst-`uname -s`-`uname -m`; \
    chmod +x /usr/local/bin/envsubst

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN usermod -u 1000 www-data

COPY entrypoint.sh /entrypoint.sh
COPY config/ /opt/wallabag/config/

RUN mkdir -p /var/www/html/app/config/

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php", "bin/console", "--env=dev", "server:run"]