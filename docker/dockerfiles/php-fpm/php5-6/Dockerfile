FROM php:5.6-fpm

ENV TZ=Europe/Paris
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV DEBIAN_FRONTEND=noninteractive

RUN addgroup dockergroup
RUN adduser --ingroup dockergroup --uid 501 dockeruser --gecos "" --disabled-password

RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime
RUN mkdir -p mkdir -p /usr/share/man/man1


COPY php.ini /usr/local/etc/php/php.ini
#COPY docker-secret-entrypoint /usr/local/bin/

RUN apt-get update && DEBIAN_FRONTEND=noninteractive && apt-get install --no-install-recommends -y  \
        locales \
        $PHPIZE_DEPS libmcrypt-dev libmemcached-dev \
        libsqlite3-dev mysql-client \
        curl git libicu-dev wget libltdl7\
        libc-client-dev \
        libxml2 libxml2-dev \
        vim \
        libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
        libmagickwand-dev \
        openjdk-8-jre ruby ruby-dev libsasl2-dev \
        openssh-client \
        libzip-dev unzip \
        libsmbclient-dev \
        libssh2-1-dev libssh2-1 \
        samba-client smbclient \
        libyaml-dev zlib1g-dev \
        && docker-php-ext-install bcmath gd intl mbstring opcache pdo pdo_mysql zip \
        && gem install sass --no-rdoc --no-ri --no-prerelease && gem install compass --no-rdoc --no-ri --no-prerelease

# Set locales
RUN dpkg-reconfigure locales
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
&& echo "fr_FR.UTF-8 UTF-8" >> /etc/locale.gen \
&& echo "en_GB.UTF-8 UTF-8" >> /etc/locale.gen \
&& echo "it_IT.UTF-8 UTF-8" >> /etc/locale.gen

WORKDIR /var/www/

RUN docker-php-ext-configure intl \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-install bcmath gd intl mbstring mcrypt opcache pcntl pdo pdo_mysql soap zip
RUN pecl install ssh2-0.13 apcu-4.0.11 memcached-2.2.0 imagick-3.4.4 yaml-1.3.1 redis-5.0.2 xdebug-2.5.5 && docker-php-ext-enable apcu mcrypt ssh2 xdebug

RUN apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $PHPIZE_DEPS \
    && rm -rf /var/lib/apt/lists/*

# Conf libs
RUN { \
        echo 'opcache.memory_consumption=1024'; \
        echo 'opcache.interned_strings_buffer=32'; \
        echo 'opcache.max_accelerated_files=49771'; \
        echo 'opcache.max_file_size=0' ; \
        echo 'opcache.revalidate_freq=2'; \
        echo 'opcache.fast_shutdown=1'; \
        echo 'opcache.enable_cli=1'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


CMD ["php-fpm", "-F"]

WORKDIR /var/www/html

EXPOSE 9000
