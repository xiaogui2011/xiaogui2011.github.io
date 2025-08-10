---
layout: post
title: remine_backup
slug: redmine_backup
author: ymkNK
date: 2025-08-10 17:27:58 +0800
categories: 
tags: 
img: 1.jpg
---

## 步骤

```shell
root@root:/tmp# cat docker-compose.yml
version: '3.8'

services:
  redmine-db:
    image: crpi-w0je8ktm7hpsmeki.cn-hangzhou.personal.cr.aliyuncs.com/aspiration/mariadb:10.5
    container_name: redmine-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: redmine
      MYSQL_USER: redmine
      MYSQL_PASSWORD: redminepass
    volumes:
      - ./db:/var/lib/mysql
    networks:
      - redmine-net

  redmine:
    # image: redmine:latest
    # image: redmine-custom-v1:latest
    # image: redmine-custom-v3:latest
    image: crpi-w0je8ktm7hpsmeki.cn-hangzhou.personal.cr.aliyuncs.com/aspiration/redmine-custom-v6:latest
    image: crpi-w0je8ktm7hpsmeki.cn-hangzhou.personal.cr.aliyuncs.com/aspiration/redmine:latest
    container_name: redmine-app
    restart: always
    ports:
      - "30001:3000"
      - "30002:3001"
    environment:
      REDMINE_DB_MYSQL: redmine-db
      REDMINE_DB_DATABASE: redmine
      REDMINE_DB_USERNAME: redmine
      REDMINE_DB_PASSWORD: redminepass
    volumes:
      - ./files:/usr/src/redmine/files
      - ./plugins:/usr/src/redmine/plugins
    networks:
      - redmine-net
    depends_on:
      - redmine-db

networks:
  redmine-net:
    driver: bridge
```


```
tar czvf redmine-backup-$(date +%F).tar.gz db files plugins

ssh -f -N -R 0.0.0.0:7890:0.0.0.0:7890 root@192.168.31.227

0 2 * * * /volume1/docker/redmine/auto_backup.sh >> /var/log/redmine_backup.log 2>&1

docker exec redmine-db sh -c 'exec mysqldump -uroot -p"$MYSQL_ROOT_PASSWORD" redmine' > redmine_backup.sql
```


```
root@DS3622xs:/volume1/docker/redmine# 0 2 * * * /volume1/docker/redmine/auto_backup.sh >> /var/log/redmine_backup^Cog 2>&1
root@DS3622xs:/volume1/docker/redmine# cat auto_backup.sh
#!/bin/bash
set -e

BACKUP_DIR="./backups"
TIMESTAMP=$(date +%F_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/redmine_data_backup_$TIMESTAMP.tar.gz"

mkdir -p "$BACKUP_DIR"

echo "Backing up db, files, plugins folders (without stopping containers) ..."
tar czvf "$BACKUP_FILE" db files plugins

# 删除7天前备份
find "$BACKUP_DIR" -type f -name "*.tar.gz" -mtime +7 -exec rm {} \;

echo "Backup done: $BACKUP_FILE"


cd /volume1/docker/redmine/backups/ & python -m http.server 8888

```

数据最后放到icloud里

```shell
cd /volume1/docker/redmine/backups/ & python -m http.server 8888
```
