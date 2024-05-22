---
layout: post
title: build-disk
slug: build-disk
author: ymkNK
date: 2024-05-22 10:57:08 +0800
categories: [云原生, docker]
tags: 
img: 1.jpg
---

打包kubesphere的前端

## dockerfile

```docker
FROM docker-hub.cloud.top/docker-hub/nginx-waf:alpine
# COPY _nginx/default.conf /etc/nginx/conf.d/default.conf
# COPY _nginx/ssl /etc/nginx/ssl
COPY default.conf /etc/nginx/conf.d/default.conf
COPY ssl /etc/nginx/ssl
RUN rm -rf /usr/share/nginx/html/*
COPY dist/ng-cloud /usr/share/nginx/html
COPY docker-entrypoint.sh /usr/bin/
RUN chmod 755 /usr/bin/docker-entrypoint.sh
EXPOSE 80
ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]
CMD [ "nginx", "-g", "daemon off;"]
```

## build.sh

```
#!/bin/bash

scp -r /tmp/dist root@10.8.15.181:/tmp
scp /home/zhang/gopath/src/license_register/shell/buildDist/dockerfile root@10.8.15.181:/tmp

ssh root@10.8.15.181 << EOF

docker pull docker-hub.cloud.top/docker-hub/nginx-waf:alpine

mkdir -p /tmp/buildDist
# shellcheck disable=SC2164
cd /tmp/buildDist

#docker exec -it licenseregister_web_1 sh
sudo docker cp licenseregister_web_1:/etc/nginx/conf.d/default.conf .
sudo docker cp licenseregister_web_1:/etc/nginx/ssl .
sudo docker cp licenseregister_web_1:/usr/bin/docker-entrypoint.sh ./

# check dist
mv /tmp/dist ./
mv /tmp/dockerfile ./

docker build -t docker-hub.cloud.top/license/web:dev ./

docker-compose -f /home/license_register/docker-compose.yaml up -d
EOF

```
