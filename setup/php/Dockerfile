FROM php:7.4-apache

RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libpq5 \
    libpq-dev \
    libsodium-dev \
    zip \
    unzip

RUN docker-php-ext-install pdo pdo_mysql pdo_pgsql sodium mbstring exif pcntl bcmath gd && \
    docker-php-ext-enable pdo_pgsql sodium

# Enable apache mods
# allow .htaccess with RewriteEngine
RUN a2enmod rewrite \
    && a2enmod headers

COPY ./setup/php/000-default.conf /etc/apache2/sites-available/000-default.conf

# COPY ./api /var/www/html/homepage-api
# Configure non-root user.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

RUN chown -R www-data:www-data /var/www
# without the following line we get "AH00558: apache2: Could not reliably determine the server's fully qualified domain name"
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf
# authorize .htaccess files
RUN sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf
# for production :
RUN echo "ServerTokens Prod\n" >> /etc/apache2/apache2.conf
RUN echo "ServerSignature Off\n" >> /etc/apache2/apache2.conf

# start Apache2 on image start
# CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
CMD /usr/sbin/apache2ctl -D FOREGROUND

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer \
    && composer --ansi --version --no-interaction

WORKDIR /var/www/depota

EXPOSE 8000