FROM wordpress:php8.2-apache

RUN apt-get update && apt-get install -y \
    unzip \
    curl \
    less \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install mysqli

EXPOSE 80