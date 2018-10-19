# Fast PHP Env

基于alpine的微型php docker环境，php 是 `7.x`。构建完成的镜像只有30-60M

共有几个镜像

- `passkey/php:7.2-alpine-base` 基础镜像，后几个镜像基于它。(含有php和一些通用的扩展)
- `passkey/php:7.2-alpine-cli` php cli环境镜像，含有swoole 和 mongodb 扩展
- `passkey/php:7.2-alpine-fpm` 在 `passkey/php:7.2-alpine-cli` 的基础上，含有 nginx php-fpm opcache

---
## 直接拉取

```bash
docker pull passkey/php:7.2-alpine-base
```

```bash
docker pull passkey/php:7.2-alpine-cli
docker pull passkey/php:7.2-alpine-fpm
```

> hub.docker 地址： https://hub.docker.com/r/passkey/php/

## 本地构建

### 构建基础镜像

```bash
docker build . -f Dockerfile -t passkey/php:7.2-alpine-base
```

### 构建功能镜像

- 构建 `passkey/php:7.2-alpine-cli`

```bash
docker build . -f Dockerfile -t passkey/php:7.2-alpine-cli
```

- 构建 `passkey/php:7.2-alpine-fpm`

```bash
docker build . -f Dockerfile -t passkey/php:7.2-alpine-fpm
```

---

### 包含的第三方扩展

- redis
- mongodb
- swoole


### 镜像中的一些信息

- php execute file: `/usr/bin/php`
- php ini file: `/etc/php7/php.ini`
- 扩展配置目录：`/etc/php7/conf.d`
- 扩展编译目录：`/usr/lib/php7/modules`
- php-fpm execute file: `/usr/bin/php-fpm`
- php-fpm conf: `/etc/php7/php-fpm.conf`
- nginx conf: `/etc/nginx/conf.d`
- 默认web目录: `/var/www`
- 默认log目录: `/var/log`
