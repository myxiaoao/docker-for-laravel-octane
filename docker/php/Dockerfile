FROM php:8.3.12-cli-alpine3.19

COPY --from=composer:2.8.1 /usr/bin/composer /usr/bin/

RUN \
    composer config -g repos.packagist composer https://packagist.jp && \
    set -ex && \
    apk update && \
    apk add --no-cache libstdc++ libpq libzip-dev gmp-dev && \
    apk add --no-cache --virtual .build-deps $PHPIZE_DEPS  curl-dev linux-headers postgresql-dev sqlite-dev openssl-dev pcre-dev pcre2-dev zlib-dev && \
    apk add --no-cache supervisor && \
    apk add --no-cache supercronic && \
    pecl channel-update pecl.php.net && \
    docker-php-ext-install zip && \
    docker-php-ext-install gmp && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install sockets && \
    docker-php-ext-install pdo_mysql && \
    pecl install --configureoptions 'enable-redis-igbinary="no" enable-redis-lzf="no" enable-redis-zstd="no"' redis-6.1.0 && \
    docker-php-ext-enable redis && \
    docker-php-source extract && \
    mkdir /usr/src/php/ext/swoole && \
    curl -sfL https://github.com/swoole/swoole-src/archive/v5.1.5.tar.gz -o swoole.tar.gz && \
    tar xfz swoole.tar.gz --strip-components=1 -C /usr/src/php/ext/swoole && \
    docker-php-ext-configure swoole \
        --enable-mysqlnd \
        --enable-swoole-pgsql \
        --enable-swoole-sqlite \
        --enable-brotli \
        --enable-openssl \
        --enable-sockets --enable-swoole-curl && \
    docker-php-ext-install -j$(nproc) swoole && \
    docker-php-ext-configure pcntl --enable-pcntl && \
    docker-php-ext-install pcntl && \
    rm -f swoole.tar.gz && \
    docker-php-source delete && \
    apk del .build-deps

# TODO not use node in producation
RUN apk add --no-cache nodejs npm git && npm config set registry https://registry.npmmirror.com

COPY docker/php/supervisord/supervisord.conf /etc/

RUN mkdir -m 446 /.composer /.npm

EXPOSE 8000

WORKDIR "/var/www/"

ENTRYPOINT ["supervisord", "--nodaemon", "--configuration", "/etc/supervisord.conf"]
