FROM nginx:stable AS builder

ENV NGX_CACHE_PURGE_VERSION=3.0.4

# install build tools
RUN apt-get update && \
    apt-get install --no-install-recommends --no-install-suggests -y \
      wget \
      ca-certificates \
      build-essential \
      libssl-dev \
      libpcre2-dev \
      zlib1g-dev && \
    rm -rf /var/lib/apt/lists/*

# download and extract sources
RUN cd /tmp && \
    wget https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz && \
    wget https://github.com/nginx-modules/ngx_cache_purge/archive/$NGX_CACHE_PURGE_VERSION.tar.gz \
         -O ngx_cache_purge.tar.gz && \
    tar -xf nginx-$NGINX_VERSION.tar.gz && \
    mv nginx-$NGINX_VERSION nginx && \
    tar -xf ngx_cache_purge.tar.gz && \
    mv ngx_cache_purge-$NGX_CACHE_PURGE_VERSION ngx_cache_purge

# build cache purge as dynamic module, binary compatible with the official nginx build
RUN cd /tmp/nginx && \
    ./configure --with-compat --add-dynamic-module=/tmp/ngx_cache_purge && \
    make modules

FROM nginx:stable

COPY --from=builder /tmp/nginx/objs/ngx_http_cache_purge_module.so /usr/lib/nginx/modules/

# change uid & gid of www-data
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data
