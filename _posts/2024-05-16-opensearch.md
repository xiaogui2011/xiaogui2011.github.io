---
layout: post
title: opensearch
slug: opensearch
author: ymkNK
date: 2024-05-16 09:41:41 +0800
categories: 中间键 数据库
tags: 中间键 数据库 opensearch
img: 1.jpg
---


## 对比


[opensearch与elasticsearch对比]()
> 但OpenSearch还包括了一些新的功能特性，如数据湖支持、实时SQL查询等。


## k8s部署opensearch


opensearch.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: opensearch-deployment
  namespace: wwmonitor
  labels:
    app: opensearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: opensearch
  template:
    metadata:
      labels:
        app: opensearch
    spec:
      containers:
      - name: opensearch-node1
        image: dockerhub.kubekey.local/wwmonitor/opensearch:2.14.0
        resources:
          limits:
            memory: "2Gi" # 这个值应等于你的JVM heap大小的两倍
        ports:
        - containerPort: 9200
        securityContext:
          capabilities:
            add:
            - IPC_LOCK
        env:
        - name: OPENSEARCH_INITIAL_ADMIN_PASSWORD
          value: "1ULUcSm^aYzY6U"
        - name: cluster.name
          value: opensearch-cluster
        - name: node.name
          value: opensearch-node1
        - name: discovery.seed_hosts
          value: opensearch-node1
        - name: cluster.initial_cluster_manager_nodes
          value: opensearch-node1
        - name: bootstrap.memory_lock
          value: "false"
        - name: OPENSEAzRCH_HOSTS
          value: '["https://opensearch-node1:9200"]'
        - name: logger.deprecation.level
          value: warn
        - name: OPENSEARCH_JAVA_OPTS
          value: "-Xms512m -Xmx512m"
```


[测试连接](https://www.instaclustr.com/support/documentation/opensearch/using-opensearch/connecting-to-opensearch-using-curl/)
```shell
curl -k -u admin:1ULUcSm^aYzY6U https://127.0.0.1:9200
```



## k8s 部署 jaeger

jaeger连接opensearch

