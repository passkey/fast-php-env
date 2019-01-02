# @description reference from https://github.com/swoft-cloud/alphp.git
# ------------------------------------------------------------------------------------
# @link https://hub.docker.com/_/alpine/      alpine image
# @link https://hub.docker.com/_/php/         php image
# @link https://github.com/docker-library/php php dockerfiles
# ------------------------------------------------------------------------------------
# @build-example docker build . -f Dockerfile -t passkey/php:7.2-alpine-cli
# ------------------------------------------------------------------------------------
# @run-container docker run -d -it --name tcli passkey/php:7.2-alpine-cli
#                docker exec -it tcli /bin/sh
# ------------------------------------------------------------------------------------

FROM passkey/php:7.2-alpine-base
LABEL maintainer="gengwh <8505569@qq.com>" version="1.0"

##
# ---------- env settings ----------
##

ENV SWOOLE_VERSION=4.2.11 \
    MONGO_VERSION=1.5.3 \
    #  install and remove building packages
    PHPIZE_DEPS="autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev"

##
# install php extensions
##

# 可以选择事先下载好
# COPY deps/hiredis-${HIREDIS_VERSION}.tar.gz hiredis.tar.gz
# COPY deps/swoole-${SWOOLE_VERSION}.tar.gz swoole.tgz
# COPY deps/mongodb-${MONGO_VERSION}.tgz mongodb.tgz
RUN set -ex \
        && cd /tmp \
        && curl -SL "https://github.com/redis/hiredis/archive/v${HIREDIS_VERSION}.tar.gz" -o hiredis.tar.gz \
        # && curl -SL "https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz" -o swoole.tgz \
	&& curl -SL "http://pecl.php.net/get/swoole-${SWOOLE_VERSION}.tgz" -o swoole.tgz \
        # && curl -SL "https://github.com/mongodb/mongo-php-driver/archive/v${MONGO_VERSION}.tgz" -o mongodb.tgz \
        && curl -SL "http://pecl.php.net/get/mongodb-${MONGO_VERSION}.tgz" -o mongodb.tgz \
        && ls -alh \
        && apk update \
        # for swoole extension libaio linux-headers
        && apk add --no-cache libstdc++ openssl \
        && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libaio-dev openssl-dev inotify-tools \
        # php extension: mongodb
        && pecl install mongodb.tgz \
        # && pecl install mongodb \
        && echo "extension=mongodb.so" > /etc/php7/conf.d/20_mongodb.ini \
        # php extension: swoole
        && cd /tmp \
        && mkdir -p swoole \
        && tar -xf swoole.tgz -C swoole --strip-components=1 \
        && rm swoole.tgz \
        && ( \
            cd swoole \
            && phpize \
            && ./configure --enable-mysqlnd --enable-openssl \
            && make -j$(nproc) && make install \
        ) \
        && rm -r swoole \
        && echo "extension=swoole.so" > /etc/php7/conf.d/20_swoole.ini \
        && php -v \
        # ---------- clear works ----------
        && apk del .build-deps \
        && rm -rf /var/cache/apk/* /tmp/* /usr/share/man \
        && echo -e "\033[42;37m Build Completed :).\033[0m\n"

EXPOSE 9501
WORKDIR /var/www
