FROM php:8.0-apache

# RUN apt-get -y update && apt-get -y upgrade
RUN apt-get -y update \
    && apt-get install -y --no-install-recommends \
    pdftk \
    libz-dev \
    build-essential \
    apache2-utils \
    libmagickwand-dev \
    imagemagick \
    libcurl4-openssl-dev \
    libssl-dev \
    libzip-dev \
    libc-client2007e-dev \
    libkrb5-dev \
    libmcrypt-dev \
    unixodbc-dev \
    ffmpeg \
    ca-certificates \
    curl \ 
    git \
    && apt-get clean \
    && rm -r /var/lib/apt/lists/*

RUN curl -Lsf 'https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz' | tar -C '/usr/local' -xvzf -
ENV PATH /usr/local/go/bin:$PATH
RUN go get github.com/mailhog/mhsendmail
RUN cp /root/go/bin/mhsendmail /usr/bin/mhsendmail

# Config Extension
RUN docker-php-ext-configure gd  \
    && docker-php-ext-configure imap --with-imap-ssl --with-kerberos \
    && docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr

# Install Extension mysqli mysql mbstring opcache pdo_mysql gd mcrypt zip imap bcmath soap pdo
RUN docker-php-ext-install mysqli pdo_mysql gd zip imap soap pdo pdo_odbc

COPY conf/wordpress.conf /etc/apache2/sites-available/wordpress.conf

COPY ./conf/php.ini $PHP_INI_DIR/php.ini

RUN chown -R www-data:www-data /var/www/html
RUN find /var/www/html -type d -exec chmod 755 {} \;
RUN find /var/www/html -type f -exec chmod 644 {} \;

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf &&\
    a2enmod rewrite &&\
    a2dissite 000-default &&\
    a2ensite wordpress &&\
    service apache2 restart