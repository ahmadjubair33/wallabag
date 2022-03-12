FROM php:8.0.16-fpm

# XXX Use UID/GID non-rootless docker containers
ARG USER_UID=1000
ARG USER_GID=1000
ARG DEBIAN_FRONTEND=noninteractive
ARG NODE_VERSION=14

RUN apt-get update \
    && apt-get install -y \
      ca-certificates \
      curl \
      gnupg \
      lsb-release \
      openssl \
      software-properties-common

RUN curl 'https://deb.nodesource.com/gpgkey/nodesource.gpg.key' | apt-key add - \
    && echo "deb https://deb.nodesource.com/node_${NODE_VERSION}.x $(lsb_release -cs) main" > /etc/apt/sources.list.d/nodesource.list

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
        build-essential \
        nodejs
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

RUN npm install -g yarn

RUN curl -L -o /usr/local/bin/envsubst https://github.com/a8m/envsubst/releases/download/v1.1.0/envsubst-`uname -s`-`uname -m`; \
    chmod +x /usr/local/bin/envsubst

COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

RUN usermod -u ${USER_UID} www-data

COPY entrypoint.sh /entrypoint.sh
COPY config/ /opt/wallabag/config/

RUN mkdir -p /var/www/html/app/config/

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php", "bin/console", "--env=dev", "server:run"]