# wordpress-docker

1. Clone repo and rename folder to project name
2. Create wordpress-docker.conf:

    MYSQL_ROOT_PASSWORD=
    MYSQL_DATABASE=
    MYSQL_USER=
    MYSQL_PASSWORD=

3. Build and start Docker containers

    docker-compose pull
    docker-compose up -d

4. Download Wordpress file to data/srv/public

    wget https://wordpress.org/latest.zip
    unzip latest.zip
    rm latest.zip
    mv wordpress/* .
    rm -r wordpress

5. Configure wp-config.php

    mv wp-config-sample.php wp-config.php

    Changes in wp-config.php:
    
        - define('DB_NAME', '<as configured>');
        - define('DB_USER', '<as configured>');
        - define('DB_PASSWORD', '<as configured>');
        - define('DB_HOST', 'mysql');

        - Unique Keys and Salts

        - define('WP_REDIS_CLIENT', 'pecl');
        - define('WP_REDIS_HOST', 'redis');

6. Enter site and do basic Wordpress setup

7. Install plugin Nginx Cache
    
    https://wordpress.org/plugins/nginx-cache/

    Settings:

        - Cache Zone Path: /var/www/cache
        - Purge Cache: Enabled

8. Install plugin Redis Object Cache

    https://wordpress.org/plugins/redis-cache/

    Settings:

        - Enable Object Cache
