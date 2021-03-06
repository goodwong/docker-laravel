FROM php:7.2-fpm-alpine

# If you're in China, or you need to change sources, will be set CHANGE_SOURCE to true in .env.
ENV CHANGE_SOURCE CN
RUN if [ ${CHANGE_SOURCE} == CN ]; then \
        # Change application source from dl-cdn.alpinelinux.org to aliyun source
        echo "change to Chinese apk source"; \
        sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    ;fi
# GD
RUN NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    &&  apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev \
    &&  docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    &&  docker-php-ext-install -j${NPROC} gd \
    &&  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev
# postgres
RUN NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    &&  apk add --no-cache libpq \
    &&  set -x \
    &&  apk add --no-cache --virtual .build-deps \
        postgresql-dev \
    &&	docker-php-ext-install pdo_pgsql \
    &&	apk del .build-deps
# zip
RUN NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && apk add --no-cache zlib-dev \
    && docker-php-ext-configure zip \
        --with-zlib-dir=/usr \
    && docker-php-ext-install -j${NPROC} zip
# pcntl
RUN NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NPROC} pcntl
# opcache
RUN apk add --no-cache pcre-dev \
    && docker-php-ext-install opcache
# conf
RUN echo -e "\npm.max_children = 50\npm.start_servers = 2\npm.min_spare_servers = 2\npm.max_spare_servers = 35\n" >> /usr/local/etc/php-fpm.d/zz-docker.conf
    # docker-php-ext-install -j5 iconv mcrypt && \
# redis
RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        coreutils \
        curl-dev \
        libedit-dev \
        libressl-dev \
        libsodium-dev \
        libxml2-dev \
        sqlite-dev && \
    yes '' | pecl install redis && \
    docker-php-ext-enable redis