FROM php:7.2-apache

ENV XDEBUG_ENABLE 0
RUN pecl config-set preferred_state beta \
  && pecl install -o -f xdebug \
  && rm -rf /tmp/pear \
  && pecl config-set preferred_state stable
  
# Install mbstring
RUN docker-php-ext-install mbstring

# Configure Apache Document Root
ENV APACHE_DOC_ROOT /var/www/html

# Enable Apache mod_rewrite
RUN a2enmod rewrite

# Copy html files
COPY ./myapp/index.php /var/www/html/index.php
CMD ["apachectl", "-D", "FOREGROUND"]