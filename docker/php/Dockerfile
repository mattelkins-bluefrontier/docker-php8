# ARG
ARG TAG=latest

# From
FROM php:${TAG}
ENV PHP_VERSION=${VERSION}

# Set timezone
ENV TZ=${TZ:-UTC}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install non-dev dependencies
RUN set -eux \
  && apk add --no-cache \
  git vim zip unzip bash curl tzdata icu-libs \
  c-client make ca-certificates imap gmp gettext libssh2 \
  libintl libxslt libpng libwebp libjpeg-turbo freetype imap \
  linux-headers oniguruma libxslt libpq vips \
  gmp libzip libxml2 freetds

# Install dependencies
RUN set -eux \
  && apk add --no-cache --virtual .build_deps \
  libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev imap-dev \
  linux-headers oniguruma-dev libxslt-dev postgresql-dev vips-dev \
  libssh2-dev gmp-dev libzip-dev libxml2-dev freetds-dev \
  $PHPIZE_DEPS \
  \
  # PHP extensions
  && docker-php-ext-install \
  mysqli \
  pdo_mysql \
  bcmath \
  mbstring \
  xml \
  gd \
  exif \
  zip \
  soap \
  intl \
  xsl \
  pcntl \
  sockets \
  sysvmsg \
  sysvsem \
  sysvshm \
  opcache \
  imap \
  gmp \
  \
  # Install xdebug
  && pecl install -o -f xdebug \
  && docker-php-ext-enable xdebug \
  \
  # Clean PHP extensions
  && rm -rf /tmp/pear \
  && docker-php-source delete \
  \
  # Clean up \
  && apk del --purge .build_deps \
  && rm -rf /var/cache/apk/*

# Install composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Copy PHP settings
COPY ./php.ini ${PHP_INI_DIR}/conf.d/99-php.ini

# Logs
ENV LOG_DIR=/var/log/php

# Tmp
RUN mkdir -p /var/tmp/php
RUN chown -R www-data:www-data /var/tmp/php
ENV TMP_DIR=/var/tmp/php

# Expose port
EXPOSE 9000

# Entrypoint
ENTRYPOINT ["php-fpm"]
