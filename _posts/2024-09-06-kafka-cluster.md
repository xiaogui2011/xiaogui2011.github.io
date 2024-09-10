---
layout: post
title: kafka-cluster
slug: kafka-cluster
author: ymkNK
date: 2024-09-10 23:10:59 +0800
categories: [开发, 中间键, kafka]
tags: [kafka, 中间键, 消息队列]
img: 1.jpg
---

## 部署单节点
[3.1 使用 KRaft 模式单机安装（SASL/PLAIN 认证方式）](https://blog.csdn.net/qq_38263083/article/details/133313443)




### kraft sasl-512 单节点
```yaml
version: "3"

services:
  kafka:
    image: 'bitnami/kafka:3.7.0'
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
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://192.168.173.129:9092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=PLAIN
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=PLAIN
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

## 部署集群
[参考视频](https://www.bilibili.com/video/BV1Ne4y1i7bg/?spm_id_from=333.337.search-card.all.click&vd_source=31e016075d5dc418e05dd62618989320)

### kraft sasl-512 集群
node1
```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node1
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=CLIENT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.36:9092
      - KAFKA_BROKER_ID=1
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=no
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SUPER_USERS=User:test
      - KAFKA_CFG_ALLOW_EVERYONE_IF_NO_ACL_FOUND=false
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456

    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```

node2
```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node2
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=CLIENT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.50:9092
      - KAFKA_BROKER_ID=2
      - KAFKA_CFG_NODE_ID=2
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=no
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SUPER_USERS=User:test
      - KAFKA_CFG_ALLOW_EVERYONE_IF_NO_ACL_FOUND=false
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```


node3
```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node3
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=CLIENT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,CLIENT:SASL_PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=CLIENT://10.130.9.87:9092
      - KAFKA_BROKER_ID=3
      - KAFKA_CFG_NODE_ID=3
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=no
      - KAFKA_CFG_SASL_ENABLED_MECHANISMS=SCRAM-SHA-512
      - KAFKA_CFG_SASL_MECHANISM_INTER_BROKER_PROTOCOL=SCRAM-SHA-512
      - KAFKA_CFG_SUPER_USERS=User:test
      - KAFKA_CFG_ALLOW_EVERYONE_IF_NO_ACL_FOUND=false
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=CLIENT
      - KAFKA_CLIENT_USERS=test
      - KAFKA_CLIENT_PASSWORDS=123456
    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```


### kraft plaintext 集群


node1
```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node1
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,CONTROLLER:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://10.130.9.36:9092
      - KAFKA_BROKER_ID=1
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=yes
    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```

node2
```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node2
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,CONTROLLER:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://10.130.9.50:9092
      - KAFKA_BROKER_ID=2
      - KAFKA_CFG_NODE_ID=2
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=yes
    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```

node3

```yaml
version: '3'
services:
  kafka-node2:
    image: bitnami/kafka:3.7.0
    user: root
    container_name: kafka-node3
    environment:
      - KAFKA_ENABLE_KRAFT=yes
      - KAFKA_CFG_PROCESS_ROLES=broker, controller
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_LISTENERS=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,CONTROLLER:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://10.130.9.87:9092
      - KAFKA_BROKER_ID=3
      - KAFKA_CFG_NODE_ID=3
      - KAFKA_KRAFT_CLUSTER_ID=manongdashu66666666666
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=1@10.130.9.36:9093,2@10.130.9.50:9093,3@10.130.9.87:9093
      - ALLOW_PLAINTEXT_LISTENER=yes
    volumes:
      - /data/kafka/kraft:/bitnami/kafka:rw
    network_mode: host
```



## 部署kafaka-ui
```yaml
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



## docker-compose
```shell
docker-compose up -d
```


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



