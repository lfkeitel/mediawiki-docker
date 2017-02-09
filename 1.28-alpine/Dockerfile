FROM alpine:3.5
MAINTAINER Lee Keitel <lfkeitel@usi.edu>

ENV TIMEZONE            America/Chicago
ENV PHP_MEMORY_LIMIT    512M
ENV MAX_UPLOAD          50M
ENV PHP_MAX_FILE_UPLOAD 200
ENV PHP_MAX_POST        100M
ENV MEDIAWIKI_VERSION 1.28
ENV MEDIAWIKI_FULL_VERSION 1.28.0

RUN	apk update && \
	apk add --update tzdata && \
	cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
	echo "${TIMEZONE}" > /etc/timezone && \
	apk add --update \
		bash \
        curl \
		php5-cli \
		php5-mcrypt \
		php5-openssl \
		php5-opcache \
		php5-gmp \
		php5-json \
		php5-dom \
		php5-pdo \
		php5-zip \
		php5-bcmath \
		php5-gd \
		php5-odbc \
		php5-pdo_mysql \
        php5-mysqli \
		php5-gettext \
		php5-bz2 \
		php5-iconv \
		php5-curl \
		php5-ctype \
        php5-intl \
        php5-xml \
		php5-apache2 && \

	# Set environments
	sed -i "s|;*date.timezone =.*|date.timezone = \"${TIMEZONE}\"|i" /etc/php5/php.ini && \
	sed -i "s|;*memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|i" /etc/php5/php.ini && \
	sed -i "s|;*upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|i" /etc/php5/php.ini && \
	sed -i "s|;*max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|i" /etc/php5/php.ini && \
	sed -i "s|;*post_max_size =.*|post_max_size = ${PHP_MAX_POST}|i" /etc/php5/php.ini && \
	sed -i "s|;*cgi.fix_pathinfo=.*|cgi.fix_pathinfo= 0|i" /etc/php5/php.ini && \
	sed -i "s|;*expose_php =.*|expose_php = off|i" /etc/php5/php.ini && \
	sed -i "s|#LoadModule rewrite_module modules/mod_rewrite.so|LoadModule rewrite_module modules/mod_rewrite.so|i" /etc/apache2/httpd.conf && \

	# Cleaning up
	mkdir -p /var/www && \
	apk del tzdata && \
	rm -rf /var/cache/apk/*

RUN { \
		echo 'opcache.revalidate_freq=0'; \
		echo 'opcache.validate_timestamps=0'; \
		echo 'opcache.max_accelerated_files=2048'; \
		echo 'opcache.memory_consumption=192'; \
		echo 'opcache.interned_strings_buffer=16'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /etc/php5/conf.d/opcache.ini

RUN MEDIAWIKI_DOWNLOAD_URL="https://releases.wikimedia.org/mediawiki/$MEDIAWIKI_VERSION/mediawiki-$MEDIAWIKI_FULL_VERSION.tar.gz"; \
    set -x; \
    mkdir -p /usr/src/mediawiki \
    && curl -fSL "$MEDIAWIKI_DOWNLOAD_URL" -o mediawiki.tar.gz \
    && tar -xf mediawiki.tar.gz -C /usr/src/mediawiki --strip-components=1 \
	&& rm mediawiki.tar.gz

COPY apache/mediawiki.conf /etc/apache2/conf.d/mediawiki-php.conf
COPY docker-entrypoint.sh /usr/local/bin/

WORKDIR /var/www/html
EXPOSE 80
VOLUME /var/www/html

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["/usr/sbin/httpd", "-DFOREGROUND", "-f", "/etc/apache2/httpd.conf"]