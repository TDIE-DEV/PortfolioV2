FROM php:8.2-apache

RUN apt-get update && apt-get install -y \
    libicu-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libzip-dev \
    unzip \
    curl \
    git \
    vim \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install pdo_mysql gd intl zip \
    && apt-get clean

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

COPY apache.conf /etc/apache2/sites-available/000-default.conf

RUN a2enmod rewrite

# First copy only the necessary files for composer install
COPY composer.json composer.lock /var/www/html/

ENV COMPOSER_ALLOW_SUPERUSER=1

# Run composer install to generate vendor and autoload files
RUN composer install --no-dev --optimize-autoloader --no-interaction

# Now copy the rest of the application files (including source code)
COPY . /var/www/html

RUN --mount=type=secret,id=_env,dst=/etc/secrets/.env cp /etc/secrets/.env /var/www/html/.env

RUN ls
RUN ls ./vendor
RUN --mount=type=secret,id=_env,dst=/etc/secrets/.env ls /etc/secrets/.env

EXPOSE 80

CMD ["apache2-foreground"]
