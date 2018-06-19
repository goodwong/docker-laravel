FROM docker-laravel-php-fpm

# COPY --from=composer /usr/bin/composer /usr/bin/composer
# 体积太大了，自己安装
# copied from official composer Dockerfile
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /tmp
ENV COMPOSER_VERSION 1.6.2
RUN apk --no-cache add zlib-dev && \
    docker-php-ext-install zip && \
    curl -s -f -L -o /tmp/installer.php https://install.phpcomposer.com/installer && \
    php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} && \
    composer --ansi --version --no-interaction && \
    rm -rf /tmp/* /tmp/.htaccess

USER www-data

ARG CHANGE_SOURCE=CN
RUN if [ ${CHANGE_SOURCE} == CN ]; then \
    # Change composer to china source
    echo "change to Chinese composer source"; \
    composer config -g repo.packagist composer https://packagist.phpcomposer.com \
;fi

ENTRYPOINT []

CMD ["/bin/sh"]