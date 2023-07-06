FROM alireaza/php-fpm:8.2.3

USER root

RUN apk --no-cache --virtual .pdo_pgsql add --update postgresql-dev \
&& apk --no-cache add postgresql-libs \
&& NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
&& docker-php-ext-install -j${NPROC} pdo pdo_pgsql pgsql \
&& apk del .pdo_pgsql

RUN apk --no-cache --virtual .redis add --update autoconf build-base \
&& if [[ -n "$http_proxy" ]] ; then pear config-set http_proxy {$http_proxy}; fi \
&& pecl install redis && docker-php-ext-enable redis \
&& apk del .redis

RUN apk --no-cache --virtual .supervisord add --update \
&& apk --no-cache add supervisor \
&& touch /etc/supervisord.conf \
&& echo "[supervisord]" >> /etc/supervisord.conf \
&& echo "nodaemon=true" >> /etc/supervisord.conf \
&& apk del .supervisord

RUN apk --no-cache --virtual .gd add --update autoconf build-base automake make g++ \
icu-dev icu-libs zlib-dev libzip-dev \
libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev \
&& apk --no-cache add libpng libwebp libjpeg-turbo freetype \
&& docker-php-ext-configure gd --enable-gd --with-jpeg --with-webp --with-freetype \
&& NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
&& docker-php-ext-install -j${NPROC} gd \
&& apk del .gd

USER $UNAME

ENTRYPOINT ["/bin/sh", "-c", "php-fpm & supervisord --configuration /etc/supervisord.conf"]
