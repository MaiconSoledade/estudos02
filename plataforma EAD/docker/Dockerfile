# PHP Base image
FROM php:7.1.33-apache

ARG INSTALL_NAME='desenv'
ENV INSTALL_NAME=${INSTALL_NAME}
ARG BUILD_DEPS='wget git curl'
ENV DEBIAN_FRONTEND=noninteractive 

# Setup Apache2 & PHP config
COPY ./docker-config/php.ini $PHP_INI_DIR/php.ini
COPY docker-config/000-default.conf /etc/apache2/sites-available/000-default.conf
RUN a2enmod rewrite

RUN echo "deb http://ftp.br.debian.org/debian jessie main" | tee -a /etc/apt/sources.list 
RUN apt-get update
RUN apt-get install apt-transport-https apt-utils ca-certificates ${BUILD_DEPS} --no-install-recommends -y

RUN wget --no-check-certificate http://www.deb-multimedia.org/pool/main/d/deb-multimedia-keyring/deb-multimedia-keyring_2016.8.1_all.deb

RUN dpkg -i deb-multimedia-keyring_2016.8.1_all.deb

RUN apt-get update
 
# Download  to install PHP extensions and dependencies
ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/

RUN chmod uga+x /usr/local/bin/install-php-extensions && sync

RUN apt-get install -qq -y \      
      zip \
      unzip \
      --no-install-recommends
  
RUN install-php-extensions \
      bcmath \
      bz2 \
      calendar \
      exif \
      gd \
      intl \
      ldap \
      mcrypt \
#      memcached \
      mysqli \
      opcache \
      pdo_mysql \
      soap \
      xsl \
      zip \
      sockets 

RUN mkdir -p /var/www/html/${INSTALL_NAME}

# Set directory permissions
RUN chmod -R 777 /var/www/html/${INSTALL_NAME}

# use your users $UID and $GID below
RUN groupadd apache-www-volume -g 1000
RUN useradd apache-www-volume -u 1000 -g 1000

RUN chown www-data:www-data -R /var/www/html
VOLUME ["/var/www/html/${INSTALL_NAME}"]

# Cleaning the image
RUN apt-get remove ${BUILD_DEPS} -y
RUN rm -rf deb-multimedia-keyring_2016.8.1_all.deb \
    /var/log/* \
    /var/lib/apt/lists/*


CMD ["apache2-foreground"]