FROM php:5.6.30

ARG APPUID
ARG APPUGID

# core Debian packages which is includes for creating an PHP environment
RUN apt-get update;               \
    apt-get install               \
          -y                      \
          --no-install-recommends \
      libonig-dev                 \
      libzip-dev                  \
      locales                     \
      unzip                       \
      wget                        \
      zlib1g-dev                  \
    ;                             \
    rm -rf /var/lib/apt/lists/*;  \
    rm -rf /var/cache/apt;        \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false

## core PHP extensions
RUN docker-php-ext-enable opcache; \
    docker-php-ext-install         \
      mbstring                     \
      sockets

RUN groupadd -g ${APPUGID} nutzer;    \
    useradd -r -l -u ${APPUID} -m -g nutzer nutzer

# composer
ENV COMPOSER_HOME /home/nutzer

RUN curl -sS https://getcomposer.org/installer |                \
       php -- --install-dir=/usr/local/bin --filename=composer  \
    && composer global require hirak/prestissimo                \
    && chown -R nutzer:nutzer $COMPOSER_HOME

RUN if [ ${APPUID} != 0 ];                               \
    then                                                 \
        groupadd -g ${APPUGID} ${DUSER};                 \
        useradd -r -l -u ${APPUID} -g ${DUSER} ${DUSER}; \
        chown -R ${DUSER}:${DUSER} $COMPOSER_HOME;       \
    fi
