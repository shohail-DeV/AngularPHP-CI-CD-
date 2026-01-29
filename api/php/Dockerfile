FROM php:8.0-apache

RUN docker-php-ext-install mysqli
RUN a2enmod rewrite

COPY . /var/www/html

# PHP syntax check (fails image build if broken)
RUN php -l connect.php
