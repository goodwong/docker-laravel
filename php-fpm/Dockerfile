FROM php:7.2-fpm-alpine

# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.
ENV CHANGE_SOURCE CN
RUN if [ ${CHANGE_SOURCE} == CN ]; then \
        # Change application source from dl-cdn.alpinelinux.org to aliyun source
        echo "change to Chinese apk source"; \
        sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    ;fi \
    # GD
    &&  apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev \
    &&  docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    &&  NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    &&  docker-php-ext-install -j${NPROC} gd \
    &&  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev \
    # postgres
    &&  apk add --no-cache libpq \
    &&  set -x \
    &&  apk add --no-cache --virtual .build-deps \
        postgresql-dev \
    &&	docker-php-ext-install pdo_pgsql \
    &&	apk del .build-deps
    # docker-php-ext-install -j5 iconv mcrypt && \
    # # redis
    # pecl install redis-3.1.0 && \
    # docker-php-ext-enable redis && \