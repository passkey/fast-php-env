# @description reference from https://github.com/kost/docker-alpine/
# ------------------------------------------------------------------------------------
# @link https://hub.docker.com/_/alpine/      alpine image
# @link https://hub.docker.com/_/php/         php image
# @link https://github.com/docker-library/php php dockerfiles
# ------------------------------------------------------------------------------------
# @build-example docker build . -f Dockerfile -t passkey/php:8.2-alpine-apache2
#                docker build --build-arg app_env=test . -f Dockerfile -t passkey/php:8.2-alpine-apache2
# ------------------------------------------------------------------------------------
# @run-container docker run -d -it --name tapache -p 17071:80 passkey/php:8.2-alpine-apache2
#                docker exec -it tapache /bin/sh
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
        # openssh  \
        # openssl  \
        tzdata \
        pcre \
        # install php82 and some extensions
        php82-apache2 \
        php82-bcmath \
        php82-bz2 \
        php82-curl \
        php82-ctype \
        php82-dom \
        php82-exif \
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
        php82-opcache \
        php82-pdo \
        php82-pdo_mysql \
        php82-pdo_sqlite \
        php82-phar \
        php82-posix \
        php82-redis \
        php82-simplexml \
        php82-sockets \
        php82-sodium \
        php82-sqlite3 \
        php82-session \
        php82-sysvshm \
        php82-sysvmsg \
        php82-sysvsem \
        php82-tokenizer \
        php82-xml \
        php82-zip \
        php82-zlib \
        php82-pcntl \
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
            echo "expose_php = off"; \
            echo "error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE"; \
            echo "[opcache]"; \
            echo "opcache.enable = 1"; \
            echo "opcache.enable_cli = 1"; \
            echo "opcache.revalidate_freq = 30"; \
            echo "opcache.save_comments = 0"; \
            echo "opcache.optimization_level = 1"; \
            echo "APP_ENV = _APP_ENV_"; \
        } | tee conf.d/99-overrides.ini \
        # - config apache
        && sed -i 's#\#ServerName www.example.com#ServerName localhost#' /etc/apache2/httpd.conf \
        && sed -i 's#ServerRoot /var/www#ServerRoot /var/apache2#' /etc/apache2/httpd.conf \
        && sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www"#g' /etc/apache2/httpd.conf \
        && sed -i 's#^<Directory "/var/www/localhost/htdocs">*#<Directory "/var/www">#g' /etc/apache2/httpd.conf \
        && sed -i 's#AllowOverride .*one#AllowOverride All#' /etc/apache2/httpd.conf \
        && sed -i 's/^#\(LoadModule rewrite_module\)/\1/' /etc/apache2/httpd.conf \
        && mv /var/www /var/apache2 \
        && mkdir -p /run/apache2 /var/www \
        # - optimize apache
        && sed -i 's#StartServers             5#StartServers              10#' /etc/apache2/conf.d/mpm.conf \
        && sed -i 's#MinSpareServers          5#MinSpareServers           10#' /etc/apache2/conf.d/mpm.conf \
        && sed -i 's#MaxSpareServers         10#MaxSpareServers           40#' /etc/apache2/conf.d/mpm.conf \
        && sed -i 's#MaxRequestWorkers      250#MaxRequestWorkers       1000#' /etc/apache2/conf.d/mpm.conf \
        && sed -i 's#MaxConnectionsPerChild   0#MaxConnectionsPerChild 20000#' /etc/apache2/conf.d/mpm.conf \
        # - config timezone
        && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
        && echo "${TIMEZONE}" > /etc/timezone \
        # - config site
        && echo "<?php phpinfo();?>" > /var/www/index.php \
        && { \
            echo "#!/bin/sh"; \
            echo "sed -i -e \"s#_APP_ENV_#\${APP_ENV}#\" /etc/php82/conf.d/99-overrides.ini"; \
            echo "for script in /var/www/docker/init_*.sh ; do"; \
            echo "  if [ -r \$script ] ; then"; \
            echo "    . \$script"; \
            echo "  fi"; \
            echo "done"; \
            echo "chown -R apache:apache /var/www"; \
            echo "#tail -F /var/log/apache2/*log &"; \
            echo "echo \"[i] Starting daemon...\""; \
            echo "httpd -D FOREGROUND"; \
        } | tee /run.sh \
        && chmod 755 /run.sh \
        && echo -e "\033[42;37m Build Completed :).\033[0m\n"

EXPOSE 80
WORKDIR /var/www

ENTRYPOINT ["/run.sh"]