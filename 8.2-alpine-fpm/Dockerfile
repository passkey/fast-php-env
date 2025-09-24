# @description reference from https://github.com/swoft-cloud/alphp.git
# ------------------------------------------------------------------------------------
# @link https://hub.docker.com/_/alpine/      alpine image
# @link https://hub.docker.com/_/php/         php image
# @link https://github.com/docker-library/php php dockerfiles
# ------------------------------------------------------------------------------------
# @build-example docker build . -f Dockerfile -t passkey/php:8.2-alpine-fpm
# ------------------------------------------------------------------------------------
# @run-container docker run -d -p 10080:80 --name tfpm passkey/php:8.2-alpine-fpm
#                docker exec -it tfpm /bin/sh
# ------------------------------------------------------------------------------------

FROM passkey/php:8.2-alpine-cli
LABEL maintainer="gengwh <8505569@qq.com>" version="1.0"

##
# ---------- building ----------
##

RUN set -ex \
        # change apk source repo
        && sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories \
        && apk update \
        && apk add --no-cache php82-fpm php82-pcntl php82-opcache php82-session nginx \
        && apk del --purge *-dev \
        && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php82 \
        # - config nginx
        && cd /etc/nginx \
        && sed -i -e "s#\user nginx;#user root;#" nginx.conf \
        && sed -i -e "s#\worker_connections 1024;#use epoll;\n\tworker_connections 65535;#" nginx.conf \
        && { \
            echo "location ~ .php {"; \
            echo "  fastcgi_pass   unix:/dev/shm/php-fpm.sock;"; \
            echo "  fastcgi_index  index.php;"; \
            echo "  fastcgi_split_path_info ^((?U).+.php)(/?.+)$;"; \
            echo "  fastcgi_param  SCRIPT_FILENAME  \$document_root\$fastcgi_script_name;"; \
            echo "  fastcgi_param  PATH_INFO        \$fastcgi_path_info;"; \
            echo "  fastcgi_param  PATH_TRANSLATED  \$document_root\$fastcgi_path_info;"; \
            echo "  include        fastcgi_params;"; \
            echo "}"; \
        } | tee fastcgi_php.conf \
        && { \
            echo "server {"; \
            echo "  listen       80;"; \
            echo "  server_name  localhost;"; \
            echo "  charset      utf-8;"; \
            echo "  index index.html index.htm index.php;"; \
            echo "  root  /var/www;"; \
            echo "  include fastcgi_php.conf;"; \
            echo "}"; \
        } | tee http.d/default.conf \
        #&& mkdir /run/nginx \
        # - config PHP-FPM
        && cd /etc/php82 \
        && { \
            echo "[global]"; \
            echo "pid = /var/run/php-fpm.pid"; \
            echo "error_log = /var/log/php82/php-fpm.log"; \
            echo "emergency_restart_threshold = 30"; \
            echo "emergency_restart_interval = 60s"; \
            echo "process_control_timeout = 5s"; \
            echo "daemonize = yes"; \
            echo "[www]"; \
            echo "listen.owner = root"; \
            echo "listen.group = root"; \
            echo "listen.mode = 0666"; \
            echo "listen = /dev/shm/php-fpm.sock"; \
            echo "user = root"; \
            echo "group = root"; \
            echo "pm = dynamic"; \
            echo "pm.max_children = 200"; \
            echo "pm.start_servers = 30"; \
            echo "pm.min_spare_servers = 20"; \
            echo "pm.max_spare_servers = 50"; \
            echo ";pm.max_requests = 20480"; \
            echo ";pm.process_idle_timeout = 10s"; \
            echo ";request_terminate_timeout = 120"; \
            echo "request_slowlog_timeout = 3"; \
            echo "slowlog = /var/log/php82/slow.log"; \
            echo "rlimit_files = 51200"; \
            echo "rlimit_core = 0"; \
            echo "catch_workers_output = yes"; \
            echo "env[APP_ENV] = _APP_ENV_"; \
        } | tee php-fpm.d/www.conf \
        && { \
            echo "expose_php = off"; \
            echo "error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE"; \
            echo "[opcache]"; \
            echo "opcache.enable = 1"; \
            echo "opcache.enable_cli = 1"; \
            echo "opcache.revalidate_freq = 30"; \
            echo "opcache.save_comments = 0"; \
            echo "opcache.optimization_level = 1"; \
            echo "APP_ENV = _APP_ENV_"; \
        } | tee -a conf.d/99-overrides.ini \
        # - config site
        && echo "<?php phpinfo();?>" > /var/www/index.php \
        && { \
            echo "#!/bin/sh"; \
            echo "sed -i -e \"s#_APP_ENV_#\${APP_ENV}#\" /etc/php82/php-fpm.d/www.conf"; \
            echo "sed -i -e \"s#_APP_ENV_#\${APP_ENV}#\" /etc/php82/conf.d/99-overrides.ini"; \
            echo "for script in /var/www/docker/init_*.sh ; do"; \
            echo "  if [ -r \$script ] ; then"; \
            echo "    . \$script"; \
            echo "  fi"; \
            echo "done"; \
            echo "nginx -g 'daemon on;'"; \
            echo "php-fpm82 -R -F"; \
        } | tee /run.sh \
        && chmod 755 /run.sh \
        && echo -e "\033[42;37m Build Completed :).\033[0m\n"

EXPOSE 9501 80
#VOLUME ["/var/www", "/data"]
WORKDIR /var/www

CMD ["/run.sh"]
