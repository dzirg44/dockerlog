FROM php:7.4-fpm-alpine as composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer
WORKDIR /composer
COPY composer.json .
COPY composer.lock .
COPY database/ database/
RUN composer install \
    --ignore-platform-reqs \
    --optimize-autoloader \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --verbose \
    --prefer-dist

FROM php:7.4-fpm-alpine

ARG  PHPREDIS=5.1.1

RUN docker-php-ext-install -j$(nproc) pdo_mysql pcntl
RUN apk add --no-cache pcre-dev $PHPIZE_DEPS \
    && pecl install redis-${PHPREDIS} \
    && docker-php-ext-enable redis.so

WORKDIR /laravel
COPY --from=composer /composer/vendor/ /laravel/vendor/
COPY . /laravel

CMD ["php-fpm"]

