FROM php:7.2-fpm

RUN apt-get update \
    && apt-get install -y --no-install-recommends gpg \
    && curl -fsSl http://nginx.org/keys/nginx_signing.key | apt-key add \
    && echo "deb http://nginx.org/packages/debian/ stretch nginx" > /etc/apt/sources.list.d/nginx.list \
    && echo "deb-src http://nginx.org/packages/debian/ stretch nginx" >> /etc/apt/sources.list.d/nginx.list

RUN apt-get update \
    && apt-get install -y --no-install-recommends --no-install-suggests nginx git supervisor sqlite3 zip unzip zlib1g-dev

RUN pecl install xdebug-2.6.1 && docker-php-ext-enable xdebug \
    && docker-php-ext-install pdo_mysql opcache zip

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && composer config -g repo.packagist composer https://packagist.laravel-china.org \
    && echo "daemon off;" >> /etc/nginx/nginx.conf \
    && apt-get remove -y --purge gpg \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/list/* /tmp/* /var/tmp/*

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

COPY dockerfiles/app/default.conf /etc/nginx/conf.d/default.conf
COPY dockerfiles/app/zz-docker.conf /usr/local/etc/php-fpm.d/zz-docker.conf
COPY dockerfiles/app/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 80

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
