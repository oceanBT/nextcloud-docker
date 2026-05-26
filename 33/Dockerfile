# https://raw.githubusercontent.com/nextcloud/docker/master/.examples/dockerfiles
FROM nextcloud:33.0.2-fpm-alpine

RUN set -ex; \
    \
    apk add --no-cache \
        ffmpeg \
        imagemagick \
        procps \
        samba-client \
#       libreoffice \
    ;

RUN set -ex; \
    \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        imap-dev \
        krb5-dev \
        openssl-dev \
        samba-dev \
        bzip2-dev \
    ; \
    \
    docker-php-ext-install \
        bz2 \
    ; \
    pecl install imap \
      --configureoptions="--with-kerberos --with-imap-ssl"; \
    pecl install smbclient; \
    docker-php-ext-enable smbclient imap; \
    \
    runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )"; \
    apk add --virtual .nextcloud-phpext-rundeps $runDeps; \
    apk del .build-deps


ENV NEXTCLOUD_UPDATE=1

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 CMD pgrep php-fpm || exit 1

