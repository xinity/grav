FROM php:7-cli

# docker uid and github oauth
ENV DOCKER_UID 1000
ENV GITHUB_OAUTH none
ENV PHP_WEB_PORT 8000
# composer setup
ENV COMPOSER_HOME "/var/www/.composer"

# install packages required and dirty fix for default-jre-headless
RUN mkdir -p /usr/share/man/man1 \
    && apt-get update -qq \
    && apt-get install -qqy --no-install-recommends \
    # apt-utils \
    # zlib1g-dev \
    # libfreetype6-dev \
    # libjpeg-dev \
    # libpng-dev \
    libcurl4-openssl-dev \
    # libicu-dev \
    # libmcrypt-dev \
    git \
    # default-jre-headless \
    # ant \
    # libxml2-dev \
    unzip \
    libmagickwand-dev \
    curl \
    && apt-get clean \
    && rm -r /var/lib/apt/lists/*
   
RUN pecl install imagick xdebug apcu mcrypt \
    && docker-php-ext-enable imagick apcu \
    && docker-php-ext-configure gd --with-jpeg-dir=/usr/lib/x86_64-linux-gnu --with-png-dir=/usr/lib/x86_64-linux-gnu --with-freetype-dir=/usr/lib/x86_64-linux-gnu \
    && docker-php-ext-install pcntl mbstring pdo_mysql zip curl gd intl exif bcmath soap sockets \ 
    && curl -sS https://getcomposer.org/installer | php -- --filename=composer --install-dir=/usr/local/bin \
    && rm -rf /tmp/pear/ /usr/src/*

RUN mkdir -p /var/www/.composer/cache \
    && composer global require 'hirak/prestissimo' \
    && composer config --global optimize-autoloader true \
    && composer config --global github-oauth.github.com $GITHUB_OAUTH \
    && chown -R www-data:www-data /var/www/.composer

COPY conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY conf/php.ini /usr/local/etc/php/php.ini

EXPOSE 8000
WORKDIR /var/www/html/grav
CMD [ "php","-S","0.0.0.0:8000","system/router.php" ]
