FROM bitnami/minideb:stretch

# Enabling https download of packages
RUN install_packages apt-transport-https ca-certificates curl

ENV NGINX_VERSION 1.15.11

# Download Nginx and Ngx cache purge source code
RUN install_packages build-essential zlib1g-dev libpcre3-dev libssl-dev libgeoip-dev gpg dirmngr nginx-common \
		&& GPG_KEYS=B0F4253373F8F6F510D42178520A9993A1C052F8 \
		&& cd /tmp \
		&& curl -O -fsSL https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz \
		&& curl -O -fsSL https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz.asc \
		&& export GNUPGHOME="$(mktemp -d)" \
		&& found=''; \
		for server in \
			ha.pool.sks-keyservers.net \
			hkp://keyserver.ubuntu.com:80 \
			hkp://p80.pool.sks-keyservers.net:80 \
			pgp.mit.edu \
		; do \
			echo "Fetching GPG key $GPG_KEYS from $server"; \
			gpg --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$GPG_KEYS" && found=yes && break; \
		done; \
		test -z "$found" && echo >&2 "error: failed to fetch GPG key $GPG_KEYS" && exit 1; \
		gpg --batch --verify nginx-$NGINX_VERSION.tar.gz.asc nginx-$NGINX_VERSION.tar.gz \
		&& rm -r "$GNUPGHOME" nginx-$NGINX_VERSION.tar.gz.asc \
		&& tar xzvf nginx-$NGINX_VERSION.tar.gz \
		&& curl -o ngx_cache_purge-2.3.tar.gz -fsSL https://github.com/FRiCKLE/ngx_cache_purge/archive/2.3.tar.gz \
		&& tar xzvf ngx_cache_purge-2.3.tar.gz

# Compile nginx from source with ngx_http_v2_module, ngx_http_realip_module and ngx_cache_purge
RUN cd /tmp/nginx-$NGINX_VERSION \
		&& ./configure --prefix=/usr/share/nginx \
		--with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat \
		-Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' \
		--with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' \
		--conf-path=/etc/nginx/nginx.conf \
		--http-log-path=/var/log/nginx/access.log \
		--error-log-path=/var/log/nginx/error.log \
		--lock-path=/var/lock/nginx.lock \
		--pid-path=/run/nginx.pid \
		--http-client-body-temp-path=/var/lib/nginx/body \
		--http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
		--http-proxy-temp-path=/var/lib/nginx/proxy  \
#		--with-debug \
		--with-pcre-jit \
		--with-ipv6 \
		--with-http_ssl_module \
#		--with-http_stub_status_module \
		--with-http_realip_module \
		--with-http_auth_request_module \
#		--with-http_addition_module \
		--with-http_geoip_module \
		--with-http_gunzip_module \
		--with-http_gzip_static_module \
		--with-http_v2_module \
#		--with-http_sub_module \
		--with-stream \
		--with-stream_ssl_module \
		--with-threads  \
		--add-module=/tmp/ngx_cache_purge-2.3 \
		&& make && make install \
		&& ln -fs /usr/share/nginx/sbin/nginx /usr/sbin/nginx \
		&& rm -r /tmp/nginx-$NGINX_VERSION \
		&& rm -r /tmp/ngx_cache_purge-2.3
#		&& adduser --system --no-create-home --shell /bin/false --group --disabled-login www-front

# Removing develop dependencies
RUN dpkg --remove build-essential zlib1g-dev libpcre3-dev libssl-dev libgeoip-dev gpg dirmngr

# Configuring nginx
#RUN mkdir -p /var/log/nginx \
#	    && chown -R www-front:www-front /var/log/nginx \
#	    && touch /var/log/nginx/error.log && chown www-front:www-front /var/log/nginx/error.log \
#	    && touch /var/log/nginx/access.log && chown www-front:www-front /var/log/nginx/access.log

CMD ["nginx", "-g", "daemon off;"]