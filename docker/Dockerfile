FROM php:7.0-apache

RUN apt-get update && apt-get install -y apt-utils build-essential ca-certificates openssl tar openssh-client \
    git \
    libssl-dev \
    #soap prerequisity
    libxml2-dev \
    #ldap prerequisity
    libldap2-dev \
    libmcrypt-dev \
    libicu-dev g++ \
    memcached zlib1g-dev \
    curl libcurl3 libcurl3-dev \
    mc

#zip extension
RUN docker-php-ext-install zip
#pdo mysql
RUN docker-php-ext-install pdo_mysql
#soap
RUN docker-php-ext-install soap
#sockets
RUN docker-php-ext-install sockets

RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu
RUN docker-php-ext-install ldap
#RUN docker-php-ext-install json
#RUN docker-php-ext-install hash
#RUN docker-php-ext-install mbstring
#RUN docker-php-ext-install tokenizer
RUN docker-php-ext-install mcrypt
RUN docker-php-ext-install intl
#RUN docker-php-ext-install curl
#RUN docker-php-ext-install phar

#redis, mongo extensions
RUN pecl install redis-3.1.1 \
    && pecl install mongodb-1.2.5 \
	&& docker-php-ext-enable redis mongodb

#memcache (@TODO after memcached-3.0.0 release use pecl instead)
RUN buildDeps=" \
                libmemcached-dev \
        " \
        && doNotUninstall=" \
                libmemcached11 \
                libmemcachedutil2 \
        " \
        && apt-get install -y $buildDeps --no-install-recommends \
        && rm -r /var/lib/apt/lists/* \
        \
        && docker-php-source extract \
        && git clone --branch php7 https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached/ \
        && docker-php-ext-install memcached \
        \
        && docker-php-source delete \
        && apt-mark manual $doNotUninstall \
        && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

#install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && chmod +x /usr/local/bin/composer

#reduce image size
RUN apt-get autoremove -y
RUN apt-get clean
RUN apt-get autoclean
RUN rm -r /var/lib/apt/lists/*

#enable apache mod rewrite
RUN a2enmod rewrite

RUN usermod -u 1000 www-data

ADD . /var/www/html/
WORKDIR /var/www/html/
