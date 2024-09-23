---
layout: post
title: kafka-single.md
slug: kafka-single.md
author: ymkNK
date: 2024-09-23 10:42:02 +0800
categories: [开发, 中间键, kafka]
tags: [kafka, 中间键, 消息队列]
img: 1.jpg
---

## 单节点 sasl-512
```
version: "3"

services:
  kafka:
    image: 'bitnami/kafka:3.7'
    container_name: kafka
    ports:
      - "9092:9092"
      - "9093:9093"
    restart: always
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CLIENT_LISTENER_NAME=CLIENT
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CFG_LISTENERS=CLIENT://:9092,CONTROLLER://:9093
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://59.208.243.212:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
    volumes:
      - "/data/docker/kafka/kafka_data:/bitnami"
    networks:
      - kafka_net


  kafka-ui:
    image: provectuslabs/kafka-ui:master
    container_name: kafka-ui
    ports:
      - "8910:8080"
    restart: always
    environment:
      - KAFKA_CLUSTERS_0_NAME=local
      - DYNAMIC_CONFIG_ENABLED=true
      - AUTH_TYPE=LOGIN_FORM
      - SPRING_SECURITY_USER_NAME=admin
      - SPRING_SECURITY_USER_PASSWORD=admin
    depends_on:
      - kafka
    networks:
      - kafka_net

networks:
  kafka_net:
    driver: bridge
```
