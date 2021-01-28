ARG PHP_VERSION=8.0

# "php" stage
FROM php:${PHP_VERSION}-fpm-alpine AS api_platform_php

# persistent / runtime deps
RUN apk add --no-cache \
		acl \
		fcgi \
		file \
		gettext \
		git \
	;

RUN set -eux; \
	apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
		icu-dev \
		libzip-dev \
#		postgresql-dev \
		mysql-dev \
		zlib-dev \
	; \
	\
	docker-php-ext-configure zip; \
	docker-php-ext-install -j$(nproc) \
	    pdo_mysql \
		intl \
#		pdo_pgsql \
		zip \
	; \

	docker-php-ext-enable \
		opcache \
	; \
	\
	runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)"; \
	apk add --no-cache --virtual .api-phpexts-rundeps $runDeps; \
	\
	apk del .build-deps

###> recipes ###
###< recipes ###

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN ln -s $PHP_INI_DIR/php.ini-production $PHP_INI_DIR/php.ini
#COPY docker/php/conf.d/api-platform.prod.ini $PHP_INI_DIR/conf.d/api-platform.ini

#COPY docker/php/php-fpm.d/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf

# https://getcomposer.org/doc/03-cli.md#composer-allow-superuser
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="${PATH}:/root/.composer/vendor/bin"
RUN composer install --optimize-autoloader --prefer-dist --no-dev --no-ansi --no-interaction --no-progress --no-scripts

RUN rm -rf containers/
RUN rm -rf kubernetes/

EXPOSE 9000

# Set working directory
WORKDIR /var/www/html