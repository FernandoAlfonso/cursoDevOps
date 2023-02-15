FROM php:8.1-apache
COPY ./html /var/www/html
RUN docker-php-ext-install mysqli

RUN mkdir -p /var/www/simplesaml
COPY ./simplesaml /var/www/simplesaml
COPY ./simplesaml/000-default.conf /etc/apache2/sites-available/000-default.conf
RUN service apache2 restart
EXPOSE 80
