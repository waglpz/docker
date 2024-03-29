FROM php:7.4-apache

ARG APPUID=0
ARG APPUGID=0


#<editor-fold desc="Setting user permissions and PHP.ini">
########################################################################################################################
# we will here do a setup an id of user and group that are identiclay to an user on host
# with the purpose to awoid any permissions trouble

RUN if [ ${APPUID} != 0 -a $(getent passwd www-data) ]; then \
        userdel -f www-data;                \
        if getent group www-data ; then     \
            groupdel www-data;              \
        fi;                                 \
        groupadd -g ${APPUGID} www-data;    \
        useradd -r -l -u ${APPUID} -g www-data www-data; \
    fi ;                                    \
    rm -rf /home/www-data

# setup default php.ini from production template
#RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
RUN cp -f "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="Core Debian packages which are included for creating minimal PHP environment">
########################################################################################################################

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
      autoconf                      \
      git                           \
      libicu-dev                    \
      libonig-dev                   \
      libzip-dev                    \
      locales                       \
      unzip                         \
      wget                          \
      zlib1g-dev                    \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="MSSQL with PHP extensions">
########################################################################################################################
# INFO:
# if installation fails then read pls to the link below and fix the installation if needed
# https://docs.microsoft.com/de-de/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server?view=sql-server-2017

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
    gnupg2 ;                        \
    rm -rf /var/lib/apt/lists/* ;   \
    rm -rf /var/cache/apt ;         \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

RUN curl https://packages.microsoft.com/keys/microsoft.asc > tt && apt-key add tt; \
    curl https://packages.microsoft.com/config/debian/11/prod.list > /etc/apt/sources.list.d/mssql-release.list

ENV ACCEPT_EULA=Y

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
        freetds-dev                 \
        libgssapi-krb5-2            \
        msodbcsql18                 \
        mssql-tools                 \
        unixodbc-dev                \
    ;                               \
    rm -rf /var/lib/apt/lists/* ;   \
    rm -rf /var/cache/apt ;         \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

RUN ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/

RUN docker-php-ext-configure pdo_dblib    \
      --with-libdir=/lib/x86_64-linux-gnu \
;                                         \
pecl install sqlsrv ;                     \
pecl install pdo_sqlsrv ;                 \
docker-php-ext-install pdo_dblib ;        \
docker-php-ext-enable                     \
    sqlsrv                                \
    pdo_sqlsrv

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="LDAP with PHP extensions">
########################################################################################################################

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
        libldap2-dev                \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false ; \
    docker-php-ext-configure ldap   \
        --with-libdir=lib/x86_64-linux-gnu/ \
    ;                               \
   docker-php-ext-install ldap

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="SMB Client with PHP-Extensions">
########################################################################################################################

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
      smbclient                     \
      libsmbclient-dev              \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false ;  \
    pecl install smbclient ;        \
    docker-php-ext-enable smbclient

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="IMAP">
########################################################################################################################

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
      libc-client-dev               \
      libkrb5-dev                   \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false ; \
    docker-php-ext-configure imap --with-kerberos --with-imap-ssl ; \
    docker-php-ext-install imap

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="Images manipulation with PHP">
########################################################################################################################

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
      libfreetype6-dev              \
      libgd-dev                     \
      libjpeg62-turbo-dev           \
      libpng-dev                    \
      libwebp-dev                   \
      libxpm-dev                    \
      libxpm-dev                    \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge                   \
      -y                            \
      --auto-remove                 \
      -o APT::AutoRemove::RecommendsImportant=false \
;                                   \
docker-php-ext-install              \
    -j$(nproc) gd                   \
;                                   \
docker-php-ext-configure gd         \
                --with-external-gd  \
                --with-freetype     \
                --with-webp         \
                --with-xpm

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="PDF/PS files manipulation">
########################################################################################################################

RUN mkdir -p /usr/share/man/man1

RUN apt-get update;                 \
    apt-get install                 \
            -y                      \
            --no-install-recommends \
        default-jdk                 \
        enscript                    \
        ghostscript                 \
        imagemagick                 \
        pdftk-java                  \
        tesseract-ocr               \
        tesseract-ocr-deu           \
    ;                               \
    rm -rf /var/lib/apt/lists/*;    \
    rm -rf /var/cache/apt;          \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false ; \
    sed -i 's/rights="none" pattern="PDF"/rights="read|write" pattern="PDF"/g' \
    /etc/ImageMagick-6/policy.xml

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="Composer for PHP">
########################################################################################################################

ENV COMPOSER_HOME /var/www/composer

RUN curl -sS https://getcomposer.org/installer |                \
       php -- --install-dir=/usr/local/bin --filename=composer  \
    && composer global require maglnet/composer-require-checker

RUN chown -R www-data:www-data $COMPOSER_HOME

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="Setup Apache webserver">
########################################################################################################################

ENV APACHE_DOCUMENT_ROOT /app/public
RUN echo ${APACHE_DOCUMENT_ROOT}
RUN a2enmod rewrite     \
    && a2enmod headers  \
    && sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf \
    && sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------

#<editor-fold desc="Enable Locale de_DE.UTF-8">
########################################################################################################################

RUN sed -i -e 's/# de_DE.UTF-8 UTF-8/de_DE.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=de_DE.UTF-8

ENV LANG de_DE.UTF-8

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


#<editor-fold desc="PHP common extensions">
########################################################################################################################

RUN docker-php-ext-enable opcache
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install intl ; docker-php-ext-configure intl --enable-intl
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install pcntl
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install sockets
RUN docker-php-ext-install zip
RUN pecl install apcu ; docker-php-ext-enable apcu

ENV GRPC_ENABLE_FORK_SUPPORT=1
ENV GRPC_POLL_STRATEGY=epoll1
RUN pecl install grpc ; docker-php-ext-enable grpc

# when needed xdebug can enabled in file php/php-ini-overrides.ini
RUN pecl install xdebug-2.9.0

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------

#<editor-fold desc="Enable Php Stan PRO Version">
########################################################################################################################

#ENV PHPSTAN_PRO_WEB_PORT=11111
#
#EXPOSE 11111

#</editor-fold>
#-----------------------------------------------------------------------------------------------------------------------


WORKDIR /app
