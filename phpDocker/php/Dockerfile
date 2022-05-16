FROM php:7.1-fpm-stretch

# Install system packages for PHP extensions
ENV DEBIAN_FRONTEND=noninteractive
ENV SWOOLE_VERSION=4.4.3
ENV REDIS_VERSION=5.0.0
ENV PHALCON_VERSION=3.2.0
ENV PTHREADS_VERSION=3.1.6

RUN apt-get update && \
    apt-get -y install wget && \
    apt-get -y install procps  

RUN wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/attic/libmcrypt/libmcrypt-2.5.7.tar.gz \
    && mkdir -p mcrypt \
    && tar -xf libmcrypt-2.5.7.tar.gz -C mcrypt --strip-components=1 \
    && rm libmcrypt-2.5.7.tar.gz \
    && ( \
    cd mcrypt \
    && ./configure \
    && make \
    && make install \
    ) \
    && rm -r mcrypt

RUN apt-get update && \
    apt-get -y install \
        gnupg2 && \
    apt-key update && \
    apt-get update && \
    apt-get -y install \
            g++ \
            git \
            curl \
            imagemagick \
            libcurl3-dev \
            libicu-dev \
            libfreetype6-dev \
            libjpeg-dev \
            libjpeg62-turbo-dev \
            libonig-dev \
            libmagickwand-dev \
            libpq-dev \
            libpng-dev \
            libxml2-dev \
	    libxslt-dev \
            libzip-dev \
            zlib1g-dev \
            default-mysql-client \
            openssh-client \
            nano \
            unzip \
            libcurl4-openssl-dev \
            libssl-dev \
        --no-install-recommends && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install PHP extensions
RUN  docker-php-ext-configure gd \
                --with-freetype=/usr/include/ \
                --with-jpeg=/usr/include/ && \
    docker-php-ext-configure bcmath && \
    docker-php-ext-install \
        bcmath \
	    curl \
        exif \
	    gettext \
        gd \
        iconv \
        intl \
        mbstring \
	    mcrypt \
	    mysqli \
        opcache \
	    pcntl \
        pdo_mysql \
        pdo_pgsql \
	    shmop \
	    soap \
	    sockets \
	    sysvsem \
	    xmlrpc \
	    xsl \
	    zip

# Install PECL extensions
RUN printf "\n" | pecl install \
        imagick \
        mongodb && \
    docker-php-ext-enable \
        imagick \
        mongodb

# Environment settings
ENV COMPOSER_ALLOW_SUPERUSER=1 \
    PHP_USER_ID=33 \
    PHP_ENABLE_XDEBUG=0 \
    PATH=/app:/app/vendor/bin:/root/.composer/vendor/bin:$PATH \
    TERM=linux \
    VERSION_PRESTISSIMO_PLUGIN=^0.3.10

# Add configuration files
COPY image-files /

# Add GITHUB_API_TOKEN support for composer
RUN chmod 755 \
        /usr/local/bin/docker-php-entrypoint \
        /usr/local/bin/composer

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --version=1.10.21 \
        --filename=composer.phar \
        --install-dir=/usr/local/bin && \
    composer clear-cache

# Install composer plugins
RUN composer global require --optimize-autoloader \
        "hirak/prestissimo:${VERSION_PRESTISSIMO_PLUGIN}" && \
    composer global dumpautoload --optimize && \
    composer clear-cache

#swoole
RUN wget -L https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O /tmp/swoole-src-${SWOOLE_VERSION}.tar.gz \
    && tar -zxvf /tmp/swoole-src-${SWOOLE_VERSION}.tar.gz -C /tmp \
    && ( cd /tmp/swoole-src-${SWOOLE_VERSION} \
    && phpize \
    && ./configure --enable-openssl \
    && make \
    && make install ) \
    && rm -rf /tmp/*

#redis
RUN wget -L https://github.com/phpredis/phpredis/archive/${REDIS_VERSION}.tar.gz -O /tmp/phpredis-${REDIS_VERSION}.tar.gz \
    && tar -zxvf /tmp/phpredis-${REDIS_VERSION}.tar.gz -C /tmp \
    && cd /tmp/phpredis-${REDIS_VERSION} \
    && mkdir -p /usr/src/php/ext \
    && mv /tmp/phpredis-${REDIS_VERSION} /usr/src/php/ext/redis \
    && docker-php-ext-install redis \
    && rm -rf /tmp/*

#phalcon
RUN wget -L https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz -O /tmp/cphalcon-${PHALCON_VERSION}.tar.gz \
    && tar -zxvf /tmp/cphalcon-${PHALCON_VERSION}.tar.gz -C /tmp \
    && cd /tmp/cphalcon-${PHALCON_VERSION}/build \
    && ./install \
    && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini \
    && rm -rf /tmp/*

#pthreads
#RUN wget -L https://github.com/krakjoe/pthreads/archive/v${PTHREADS_VERSION}.tar.gz -O /tmp/pthreads-${PTHREADS_VERSION}.tar.gz \
#    && tar -zxvf /tmp/pthreads-${PTHREADS_VERSION}.tar.gz -C /tmp \
#    && cd /tmp/pthreads-${PTHREADS_VERSION} \
#    && phpize \
#    && ./configure \
#    && make \
#    && make install \
#    && docker-php-ext-enable pthreads \
#    && rm -rf /tmp/*

# Enable mod_rewrite for images with apache
RUN if command -v a2enmod >/dev/null 2>&1; then \
        a2enmod rewrite headers \
    ;fi
