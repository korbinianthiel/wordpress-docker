FROM php:8.5-fpm-alpine

# install extensions required/recommended by WordPress
COPY --from=mlocati/php-extension-installer:2 /usr/bin/install-php-extensions /usr/local/bin/
RUN install-php-extensions \
      bcmath \
      exif \
      gd \
      imagick \
      intl \
      mysqli \
      opcache \
      redis \
      zip

# change uid & gid of www-data
RUN apk add --no-cache shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data
