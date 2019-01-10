# @description reference from https://github.com/swoft-cloud/alphp.git
# ------------------------------------------------------------------------------------
# @link https://hub.docker.com/_/alpine/      alpine image
# @link https://hub.docker.com/_/php/         php image
# @link https://github.com/docker-library/php php dockerfiles
# ------------------------------------------------------------------------------------
# @build-example docker build . -f Dockerfile -t passkey/php:7.2-alpine-base
#                docker build --build-arg app_env=test . -f Dockerfile -t passkey/php:7.2-alpine-base
# ------------------------------------------------------------------------------------
# @run-container docker run -d -it --name tbase passkey/php:7.2-alpine-base
#                docker exec -it tbase /bin/sh
# ------------------------------------------------------------------------------------

FROM alpine:3.8
LABEL maintainer="gengwh <8505569@qq.com>" version="1.0"

##
# ---------- env settings ----------
##

# --build-arg timezone=Asia/Shanghai
ARG timezone
# prod grey test dev
ARG app_env=prod

ENV APP_ENV=${app_env:-"prod"} \
    TIMEZONE=${timezone:-"Asia/Shanghai"}

##
# ---------- building ----------
##

RUN set -ex \
        # change apk source repo
        && sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/' /etc/apk/repositories \
        && apk update \
        && apk add --no-cache \
        # Install base packages ('ca-certificates' will install 'nghttp2-libs')
        ca-certificates \
        curl \
        tar \
        xz \
        libressl \
        # openssh  \
        # openssl  \
        tzdata \
        pcre \
        # install php7 and some extensions
        php7 \
        # php7-common \
        php7-bcmath \
        php7-bz2 \
        php7-curl \
        php7-ctype \
        php7-dom \
        php7-fileinfo \	
        # php7-gettext \
        php7-gd \
        php7-iconv \
	# php7-imagick \
        php7-json \
        php7-mbstring \
	php7-mongodb \
        php7-mysqlnd \
        php7-mysqli \
        php7-openssl \
        # php7-opcache \
        php7-pdo \
        php7-pdo_mysql \
        php7-pdo_sqlite \
        php7-phar \
        php7-posix \
        php7-redis \
        php7-simplexml \
        php7-sockets \
        php7-sodium \
        # php7-sqlite \
        # php7-session \
        php7-sysvshm \
        php7-sysvmsg \
        php7-sysvsem \
        php7-tokenizer \
	php7-xml \
        php7-zip \
        php7-zlib \
        && apk del --purge *-dev \
        && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 \
        #  ---------- some config,clear work ----------
        && cd /etc/php7 \
        # - config PHP
        && { \
            echo "upload_max_filesize=100M"; \
            echo "post_max_size=108M"; \
            echo "memory_limit=1024M"; \
            echo "date.timezone=${TIMEZONE}"; \
        } | tee conf.d/99-overrides.ini \
        # - config timezone
        && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
        && echo "${TIMEZONE}" > /etc/timezone \
        && echo -e "\033[42;37m Build Completed :).\033[0m\n"

WORKDIR /var/www
