FROM composer:latest AS vendor

WORKDIR /app

COPY src/composer.json src/composer.lock ./

RUN composer install \
    --no-dev \
    --no-scripts \
    --no-autoloader \
    --prefer-dist \
    --no-interaction

COPY src/ .
RUN composer dump-autoload --optimize --no-dev

FROM php:8.4-fpm-alpine

RUN apk add --no-cache \
        libpng \
        libjpeg-turbo \
        freetype \
        libxml2 \
        oniguruma \
    && apk add --no-cache --virtual .build-deps \
        libpng-dev \
        libjpeg-turbo-dev \
        freetype-dev \
        libxml2-dev \
        oniguruma-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
        pdo \
        pdo_mysql \
        mbstring \
        exif \
        pcntl \
        bcmath \
        gd \
        opcache \
    && apk del .build-deps

WORKDIR /var/www

COPY --from=vendor /app /var/www

RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache
