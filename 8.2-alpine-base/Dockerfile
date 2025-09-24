# @description reference from https://github.com/swoft-cloud/alphp.git
# ------------------------------------------------------------------------------------
# @link https://hub.docker.com/_/alpine/      alpine image
# @link https://hub.docker.com/_/php/         php image
# @link https://github.com/docker-library/php php dockerfiles
# ------------------------------------------------------------------------------------
# @build-example docker build . -f Dockerfile -t passkey/php:8.2-alpine-base
#                docker build --build-arg app_env=test . -f Dockerfile -t passkey/php:8.2-alpine-base
# ------------------------------------------------------------------------------------
# @run-container docker run -d -it --name tbase passkey/php:8.2-alpine-base
#                docker exec -it tbase /bin/sh
# ------------------------------------------------------------------------------------

FROM alpine:3.22.1
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
        && sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories \
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
        # install php82 and some extensions
        php82 \
        # php82-common \
        php82-bcmath \
        php82-bz2 \
        php82-curl \
        php82-ctype \
        php82-dom \
        php82-fileinfo \
        # php82-gettext \
        php82-gd \
        php82-iconv \
        # php82-imagick \
        php82-json \
        php82-mbstring \
        php82-mongodb \
        php82-mysqlnd \
        php82-mysqli \
        php82-openssl \
        # php82-opcache \
        php82-pdo \
        php82-pdo_mysql \
        php82-pdo_sqlite \
        php82-phar \
        php82-posix \
        php82-redis \
        php82-simplexml \
        php82-sockets \
        php82-sodium \
        # php82-sqlite \
        # php82-session \
        php82-sysvshm \
        php82-sysvmsg \
        php82-sysvsem \
        php82-tokenizer \
        php82-xml \
        php82-zip \
        php82-zlib \
        && apk del --purge *-dev \
        && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php82 \
        #  ---------- some config,clear work ----------
        && cd /etc/php82 \
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
