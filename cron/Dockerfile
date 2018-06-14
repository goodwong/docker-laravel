FROM app_php-fpm

# USER www-data

RUN echo '* * * * * /var/www/html/artisan schedule:run >> /dev/null 2>&1' > /etc/crontabs/www-data

ENTRYPOINT []

CMD ["crond", "-f"]