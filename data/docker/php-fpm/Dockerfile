FROM hermsi/alpine-fpm-php

# change uid & gid of www-data
RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data