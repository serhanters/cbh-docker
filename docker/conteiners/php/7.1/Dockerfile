FROM php:7.1-fpm
MAINTAINER S.Rashin sar@callbackhunter.com

#
RUN apt-get update -y && \
    apt-get install -y \
      git \
      subversion \
      geoip-database \
      geoip-bin \
      libgeoip1 \
      wget \
      curl \
      g++ \
      apt-utils \
      openssl \
      tree

# Install opcache
RUN docker-php-ext-install opcache

# Install APCu
RUN pecl install apcu && echo "extension=apcu.so" > /usr/local/etc/php/conf.d/apcu.ini

# Install bcmath
RUN docker-php-ext-install bcmath

# Install bz2
RUN apt-get install -y libbz2-dev && docker-php-ext-install bz2

# Install zip
RUN apt-get install -y zlib1g-dev && docker-php-ext-install zip

# Install calendar
RUN docker-php-ext-install calendar

# Install dba
RUN docker-php-ext-install dba

# Install GD
RUN apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng12-dev && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install gd

# strings
RUN docker-php-ext-install gettext mbstring

# Install mcrypt
RUN apt-get install -y libmcrypt-dev && docker-php-ext-install mcrypt

# Install PDO
RUN apt-get install -y libpq-dev sqlite3 libsqlite3-dev \
  && docker-php-ext-install pdo_pgsql pdo_mysql pdo_sqlite

# Install phpredis
RUN git clone -b php7 https://github.com/phpredis/phpredis.git /usr/src/php/ext/redis \
  && docker-php-ext-install redis

# imagick
RUN runtimeRequirements="libmagickwand-6.q16-dev --no-install-recommends" \
  && apt-get update && apt-get install -y ${runtimeRequirements} \
  && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin/ \
  && pecl install imagick-3.4.1 \ && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini \
  && rm -rf /var/lib/apt/lists/*

# soap
RUN buildRequirements="libxml2-dev" \
  && apt-get update && apt-get install -y ${buildRequirements} \
  && docker-php-ext-install soap \
  && apt-get purge -y ${buildRequirements} \
  && rm -rf /var/lib/apt/lists/*

# yaml
RUN buildRequirements="libyaml-dev" \
  && apt-get update && apt-get install -y ${buildRequirements} \
  && pecl install yaml \
  && echo "extension=yaml.so" > /usr/local/etc/php/conf.d/ext-yaml.ini \
  && apt-get purge -y ${buildRequirements} \
  && rm -rf /var/lib/apt/lists/*

# Install XSL
RUN apt-get update -y && apt-get install -y libxslt-dev && docker-php-ext-install xsl
    
# Install xmlrpc
RUN docker-php-ext-install xmlrpc

# Install mongo
RUN pecl install mongodb-beta && echo "extension=mongo.so" > /usr/local/etc/php/conf.d/mongo.ini

# ftp
RUN apt-get update \
    && apt-get install -y libssl-dev \
    && docker-php-ext-install ftp

# ssh2
RUN apt-get update \
    && apt-get install -y libssh2-1-dev

# others
RUN docker-php-ext-install \
    soap \
    sockets \
    sysvmsg \
    sysvsem \
    sysvshm

# Install intl
RUN apt-get install -y libicu-dev && pecl install intl && docker-php-ext-install intl

# memcache libmemcached
RUN apt-get install -y \
        libcurl4-nss-dev \
        libmemcached-dev \
        libmemcached11 \
    && docker-php-ext-install iconv \
    && docker-php-ext-install curl \
    && docker-php-ext-install mysqli \
    && cd /tmp \
    && curl -L 'https://github.com/EasyFreeHost/pecl-memcache/archive/81b1267413662dfc276f4530122cd1e7aba5c1fc.tar.gz' | tar xz \
    && cd pecl-memcache-81b1267413662dfc276f4530122cd1e7aba5c1fc \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && cd .. \
    && rm -Rf pecl-memcache-81b1267413662dfc276f4530122cd1e7aba5c1fc \
&& docker-php-ext-enable memcache

# Install Memcached for php 7
RUN curl -L -o /tmp/memcached.tar.gz "https://github.com/php-memcached-dev/php-memcached/archive/php7.tar.gz" \
    && mkdir -p /usr/src/php/ext/memcached \
    && tar -C /usr/src/php/ext/memcached -zxvf /tmp/memcached.tar.gz --strip 1 \
    && docker-php-ext-configure memcached \
    && docker-php-ext-install memcached \
    && rm /tmp/memcached.tar.gz

# Install Xdebug ADD php.ini /usr/local/etc/php/php.ini
RUN curl -fsSL 'https://xdebug.org/files/xdebug-2.4.0.tgz' -o xdebug.tar.gz \
    && mkdir -p xdebug \
    && tar -xf xdebug.tar.gz -C xdebug --strip-components=1 \
    && rm xdebug.tar.gz \
    && ( \
        cd xdebug \
        && phpize \
        && ./configure --enable-xdebug \
        && make -j$(nproc) \
        && make install \
    ) \
    && rm -r xdebug \
    && docker-php-ext-enable xdebug \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

# Install composer and put binary into $PATH
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

# Install PHP Code sniffer
RUN curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcs.phar \
    && chmod 755 phpcs.phar \
    && mv phpcs.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/phpcs.phar /usr/local/bin/phpcs \
    && curl -OL https://squizlabs.github.io/PHP_CodeSniffer/phpcbf.phar \
    && chmod 755 phpcbf.phar \
    && mv phpcbf.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/phpcbf.phar /usr/local/bin/phpcbf

# Install PHPUnit
RUN curl -OL https://phar.phpunit.de/phpunit.phar \
    && chmod 755 phpunit.phar \
    && mv phpunit.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/phpunit.phar /usr/local/bin/phpunit



####### NodeJS, NPM, GRUNT, BOWER #######
# Install NodeJS
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
  && apt-get install -y nodejs

# Install Yarn
RUN apt-key adv --keyserver pgp.mit.edu --recv D101F7899D41F3C3 \
  && echo "deb http://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
  && apt-get update && apt-get install -y yarn

# Install Grunt globally
RUN npm install -g grunt-cli

# Install Gulp globally
RUN npm install -g gulp-cli

# Install Bower globally
RUN npm install -g bower
####### NodeJS, NPM, GRUNT, BOWER #######

# Symfony installer
RUN mkdir -p /usr/local/bin && \
    curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony && \
    chmod a+x /usr/local/bin/symfony

# Laravel installer
RUN composer global require "laravel/installer"

EXPOSE 9000

WORKDIR /var/www/