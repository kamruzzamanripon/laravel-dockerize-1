FROM php:8.3-fpm-alpine

# Install essential packages and PHP extensions
RUN apk add --no-cache \
    bash \
    build-base \
    zlib-dev \
    curl \
    git \
    libzip-dev \
    libpng-dev \
    freetype-dev \
    libjpeg-turbo-dev \
    icu-dev \
    postgresql-dev \
    unzip \
    vim \
    procps \
    autoconf \
    && docker-php-ext-install zip pdo_mysql pdo_pgsql pgsql \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd

# Install Redis extension (with autoconf dependency)
RUN apk add --no-cache g++ make \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && apk del g++ make  # Clean up build dependencies

# Install PCOV (with autoconf dependency)
RUN apk add --no-cache g++ make \
    && pecl install pcov \
    && docker-php-ext-enable pcov \
    && apk del g++ make  # Clean up build dependencies

# Xdebug (optional)
# RUN pecl install xdebug \
#     && docker-php-ext-enable xdebug \
#     && echo ";zend_extension=xdebug" > /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Install Node.js, NPM, and Yarn
RUN apk add --no-cache \
    nodejs \
    npm \
    && npm install npm@latest -g \
    && npm install yarn -g

# Install Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer

# Set environment variables for Composer
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV PATH $PATH:/composer/vendor/bin

# Configure Composer
RUN composer config --global process-timeout 3600 \
    && composer global require "laravel/installer"

# Clone Solarized dircolors
WORKDIR /root
RUN git clone https://github.com/seebi/dircolors-solarized

# Expose Vite's default port
EXPOSE 5173

# Set the working directory
WORKDIR /var/www
