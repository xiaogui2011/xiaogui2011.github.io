---
layout: post
title: kafka-cluster
slug: kafka-cluster
author: ymkNK
date: 2024-09-06 15:12:59 +0800
categories: 
tags: 
img: 1.jpg
---


## 集群部署

### 离线docker部署

### 离线配置文件

## docker-compose
### node1
```yaml
version: '2'

services:
  kafka:
    image: 'bitnami/kafka:3.7.0'
    container_name: kafka-1
    ports:
      - "9092:9092"  # Kafka 客户端通信端口
      - "9093:9093"  # Kafka Controller 通信端口
    restart: always
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CLIENT_LISTENER_NAME=CLIENT
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CFG_LISTENERS=CLIENT://:9092,CONTROLLER://:9093
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.36:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
      - KAFKA_LOG_RETENTION_HOURS=24
      - KAFKA_LOG_SEGMENT_BYTES=1073741824
      - KAFKA_LOG_RETENTION_BYTES=10737418240
      - KAFKA_CFG_MESSAGE_MAX_BYTES=104857600
      - KAFKA_CFG_REPLICA_FETCH_MAX_BYTES=104857600
      - KAFKA_CFG_SOCKET_REQUEST_MAX_BYTES=104857600
    volumes:
      - "/data/docker/kafka/kafka_data:/bitnami"
    networks:
      - kafka_net

networks:
  kafka_net:
    driver: bridge
```


### node2
```yaml
version: '2'

services:
  kafka:
    image: 'bitnami/kafka:3.7.0'
    container_name: kafka-1
    ports:
      - "9092:9092"  # Kafka 客户端通信端口
      - "9093:9093"  # Kafka Controller 通信端口
    restart: always
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CLIENT_LISTENER_NAME=CLIENT
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CFG_LISTENERS=CLIENT://:9092,CONTROLLER://:9093
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.36:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
      - KAFKA_LOG_RETENTION_HOURS=24
      - KAFKA_LOG_SEGMENT_BYTES=1073741824
      - KAFKA_LOG_RETENTION_BYTES=10737418240
      - KAFKA_CFG_MESSAGE_MAX_BYTES=104857600
      - KAFKA_CFG_REPLICA_FETCH_MAX_BYTES=104857600
      - KAFKA_CFG_SOCKET_REQUEST_MAX_BYTES=104857600
    volumes:
      - "/data/docker/kafka/kafka_data:/bitnami"
    networks:
      - kafka_net

networks:
  kafka_net:
    driver: bridge
```



### node3

```shell
version: '2'

services:
  kafka:
    image: 'bitnami/kafka:3.7.0'
    container_name: kafka-3
    ports:
      - "9092:9092"
      - "9093:9093"
    restart: always
    environment:
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_NODE_ID=3
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CLIENT_LISTENER_NAME=CLIENp - 4 as T
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CFG_LISTENERS=CLIENT://:9092,CONTROLLER://:9093
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.87:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
      - KAFKA_LOG_RETENTION_HOURS=24
      - KAFKA_LOG_SEGMENT_BYTES=1073741824
      - KAFKA_LOG_RETENTION_BYTES=10737418240
      - KAFKA_CFG_MESSAGE_MAX_BYTES=104857600
      - KAFKA_CFG_REPLICA_FETCH_MAX_BYTES=104857600
      - KAFKA_CFG_SOCKET_REQUEST_MAX_BYTES=104857600
    volumes:
      - "/data/docker/kafka/kafka_data:/bitnami"
    networks:
      - kafka_net

networks:
  kafka_net:
    driver: bridge
```

## 文件夹和权限
```shell
mkdir -p /data/docker/kafka/kafka_data
chown -R root:root /data/docker/kafka/kafka_data
chmod -R 777 /data/docker/kafka/kafka_data
docker-compose -f kafka.yaml up -d
```

## 认证和安全机制配置
```shell
[root@worker11 kafka]# cat kafka-server.properties
listeners=SASL_PLAINTEXT://0.0.0.0:9093
advertised.listeners=SASL_PLAINTEXT://localhost:9093
listener.security.protocol.map=SASL_PLAINTEXT:SASL_PLAINTEXT
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.enabled.mechanisms=PLAIN
sasl.mechanism.inter.broker.protocol=PLAIN

# 配置 Kafka 的 SASL/PLAIN 认证
authorizer.class.name=kafka.security.auth.SimpleAclAuthorizer
```


## 传输镜像和配置




## 执行命令
```shell
docker-compose up -d
```









### server




## 配置以及测试

### 生产者
```shell
bootstrap.servers=10.130.9.36:9092,10.130.9.50:9092,10.130.9.87:9092
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="test" password="123456";
```


### 消费者
```shell
bootstrap.servers=10.130.9.36:9092,10.130.9.50:9092,10.130.9.87:9092
group.id=test-consumer-group
security.protocol=SASL_PLAINTEXT
sasl.mechanism=SCRAM-SHA-512
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="test" password="123456";
```


### 生产者发送数据
```shell
kafka-console-producer.sh --broker-list 10.130.9.36:9092 --topic test --producer.config /path/to/client.properties
```


### 消费者消费数据
```shell
kafka-console-consumer.sh --bootstrap-server 10.130.9.36:9092 --topic test --from-beginning --consumer.config /path/to/client.properties
```



