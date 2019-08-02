---
title: "Add Xdebug to Alpine Linux Docker Container"
date: "2018-01-17 15:44:00"
type: "post"
slug: "alpine-docker-xdebug"
isCJKLanguage: true
---

原本想在以 Docker 建立的 PHP container 裡加上 Xdebug，但是單純在 Dockerfile 裡下 `pecl install xdebug` 指令的話，會發生找不到 autoconf 套件的錯誤

所使用的 Docker image 是官方的 `php:7.2.1-fpm-alpine3.7`

透過網路搜尋到的資料，大概知道了要先把必須的套件裝上，才能把 Xdebug 加進 container 裡。而這些必須的套件其實在官方的 image 裡都有裝，只是官方的 image 在用完這些套件之後，就把這些套件刪除以節省空間了[^1]

這也是 Alpine Linux 節省空間的小秘訣[^2]：

```shell
$ apk add --virtual mypacks gcc vim
$ apk del mypacks
```

## 1. 先把 `$PHPIZE_DEPS` 定義的套件裝上[^3]

```shell
$ apk add --no-cache --virtual .build-deps $PHPIZE_DEPS
```

## 2. 安裝 Xdebug

因為 Xdebug 目前最新的 stable 版本 2.5.5 還不支援 PHP 7.2，所以要指定安裝為 beta 版[^4]：

```shell
$ pecl install xdebug-2.6.0beta1
```

2018-03-21 edit: 現在 Xdebug 2.6.0 的 stable 版本已經釋出，所以之後不用再指定版本了

## 3. 再把 `$PHPIZE_DEPS` 移除以節省空間

```shell
$ apk del .build-deps
```

最後結果：

* [Add Xdebug · gundamew/bsdock@fba908e · GitHub](https://github.com/gundamew/bsdock/commit/fba908efd847ad13fc37faf7991005afdfd45bf7)
* [Remove the packages no more needed · gundamew/bsdock@d40d9c5 · GitHub](https://github.com/gundamew/bsdock/commit/d40d9c597751dc5101e79bc59c3eef421f63df2d)

[^1]: [php/Dockerfile at f4baf0edbc4e05e241938c68bcc7c9635707583d · docker-library/php · GitHub](https://github.com/docker-library/php/blob/f4baf0edbc4e05e241938c68bcc7c9635707583d/7.2/alpine3.7/fpm/Dockerfile)
[^2]: [docker - What is .build-deps for apk add --virtual command? - Stack Overflow](https://stackoverflow.com/a/46222036)
[^3]: [How do I install XDebug? · Issue #244 · docker-library/wordpress · GitHub](https://github.com/docker-library/wordpress/issues/244#issuecomment-337713822)
[^4]: [php-fpm 7.2 and xdebug as of 2018-01-10 · Issue #560 · docker-library/php · GitHub](https://github.com/docker-library/php/issues/560#issue-287597039)
