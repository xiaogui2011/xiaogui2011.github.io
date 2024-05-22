---
layout: post
title: macos安装docker
slug: macos安装docker
author: ymkNK
date: 2024-05-22 10:36:09 +0800
categories: [云原生, docker]
tags: 
img: 1.jpg
---


## orbstack

[Docker Installation](https://ddev.readthedocs.io/en/stable/users/install/docker-installation/)

```
brew install orbstack
```

<mark style="color:red;">比官方的消耗的资源小</mark>

```sh
(base) 0 16:55:53 z:~ $docker ps
Cannot connect to the Docker daemon at unix:///Users/zhangzhiyuan/.orbstack/run/docker.sock. Is the docker daemon running?
(base) 0 16:55:54 z:~ $docker context show
orbstack
(base) 0 16:55:58 z:~ $
```

## 切换docker官方desktop

```sh
docker context use desktop-linux
```

##

## Brew

brew reinstall docker

brew install --cask docker

brew uninstall docker

[Cannot connect to the Docker daemon on macOS](https://stackoverflow.com/questions/44084846/cannot-connect-to-the-docker-daemon-on-macos)

<mark style="color:red;">docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?. See 'docker run --help'.</mark>

```
brew install --cask docker
```

> Because docker is a system-level package, you cannot install it using `brew install`, and must use `--cask` instead.



## Colima

[替代 docker-desktop，Colima 来了](https://www.bilibili.com/video/BV16L4y1G7bS/?spm\_id\_from=333.337.search-card.all.click\&vd\_source=31e016075d5dc418e05dd62618989320)

```
brew install colima
colima list
colima start
docker run xxx
colima stop
```

```
docker ps 
docker context show
```

##
