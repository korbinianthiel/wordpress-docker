FROM hermsi/alpine-fpm-php

# change uid & gid of www-data to match nginx process
RUN apk add shadow && usermod -u 33 www-data && groupmod -g 33 www-data