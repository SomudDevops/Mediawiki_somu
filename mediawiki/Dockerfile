# Base image
FROM php:7.4-apache

# Set working directory
WORKDIR /var/www/html

# Copy the MediaWiki source code to the container
COPY . .

# Install dependencies
RUN apt-get update && \
    apt-get install -y \
        git \
        imagemagick \
        libicu-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libzip-dev \
        unzip \
        zip \
    && docker-php-ext-configure gd --with-jpeg \
    && docker-php-ext-install -j$(nproc) \
        exif \
        gd \
        intl \
        mysqli \
        opcache \
        zip

# Enable Apache modules
RUN a2enmod rewrite

# Set recommended PHP settings
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
RUN sed -i 's/memory_limit = .*/memory_limit = 128M/' "$PHP_INI_DIR/php.ini"
RUN echo "date.timezone = UTC" >> "$PHP_INI_DIR/php.ini"

# Set the ownership and permissions for the MediaWiki files
RUN chown -R www-data:www-data .
RUN chmod 755 .

# Expose port 80
EXPOSE 80

# Start Apache web server
CMD ["apache2-foreground"]

