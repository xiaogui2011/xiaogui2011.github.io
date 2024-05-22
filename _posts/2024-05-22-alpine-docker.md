---
layout: post
title: alpine-docker
slug: alpine-docker
author: ymkNK
date: 2024-05-22 10:58:41 +0800
categories: [云原生, docker]
tags: 
img: 1.jpg
---

## 查看系统版本

```sh
~/ai-monitor-data/clean/internal #  cat /etc/os-release
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.19.1
PRETTY_NAME="Alpine Linux v3.19"
HOME_URL="https://alpinelinux.org/"
BUG_REPORT_URL="https://gitlab.alpinelinux.org/alpine/aports/-/issues"
```

## [alpine安装软件](https://blog.csdn.net/u013288190/article/details/109673951)

```sh
~/ai-monitor-data/clean/internal # apk add --update vim
fetch https://dl-cdn.alpinelinux.org/alpine/v3.19/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.19/community/x86_64/APKINDEX.tar.gz
(1/5) Installing vim-common (9.0.2127-r0)
(2/5) Installing xxd (9.0.2127-r0)
(3/5) Installing ncurses-terminfo-base (6.4_p20231125-r0)
(4/5) Installing libncursesw (6.4_p20231125-r0)
(5/5) Installing vim (9.0.2127-r0)
Executing busybox-1.36.1-r15.trigger
OK: 39 MiB in 21 packagesshell
```
