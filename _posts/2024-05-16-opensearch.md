---
layout: post
title: opensearch
slug: opensearch
author: ymkNK
date: 2024-05-16 09:41:41 +0800
categories: 云原生 k8s
tags: k8s 中间键 数据库 opensearch
img: 1.jpg
---


## 对比


[opensearch与elasticsearch对比]()
> 但OpenSearch还包括了一些新的功能特性，如数据湖支持、实时SQL查询等。


## k8s部署opensearch

- 安装OpenSearch和OpenSearch Dashboards，可以参考OpenSearch官方文档。
- 启动OpenSearch服务和OpenSearch Dashboards服务。

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


## k8s部署opensearch dashboard
```yaml
apiVersion: v1
kind: Service
metadata:
  name: jaeger-dashboard
  namespace: default
spec:
  type: NodePort
  selector:
    app: jaeger
  ports:
  - port: 80
    target
    : 9200
    protocol: TCP
    name: http
status:
  loadBalancer: {}
```

## k8s部署jaeger
jaeger-deployment.yaml

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: jaegerAndOpensearch

---
apiVersion: v1
kind: Service
metadata:
  name: jaeger
  namespace: jaegerAndOpensearch
spec:
  ports:
    - port: 5775
      protocol: UDP
      targetPort: 5775
      name: jaeger-agent-compact
    - port: 6831
      protocol: UDP
      targetPort: 6831
      name: jaeger-agent-binary
    - port: 6832
      protocol: UDP
      targetPort: 6832
      name: jaeger-agent-compact
    - port: 5778
      protocol: TCP
      targetPort: 5778
      name: jaeger-agent-config
    - port: 16686
      protocol: TCP
      targetPort: 16686
      name: jaeger-query
    - port: 14268
      protocol: TCP
      targetPort: 14268
      name: jaeger-collector-http
    - port: 14250
      protocol: TCP
      targetPort: 14250
      name: jaeger-collector-grpc
    - port: 9411
      protocol: TCP
      targetPort: 9411
      name: zipkin
  selector:
    app: jaeger

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaegerAndOpensearch 
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
      - name: jaeger
        image: dockerhub.kubekey.local/wwmonitor/all-in-one:latest
        ports:
        - containerPort: 5775
          protocol: UDP
        - containerPort: 6831
          protocol: UDP
        - containerPort: 6832
          protocol: UDP
        - containerPort: 5778
          protocol: TCP
        - containerPort: 16686
          protocol: TCP
        - containerPort: 14268
          protocol: TCP
        - containerPort: 14250
          protocol: TCP
        - containerPort: 9411
          protocol: TCP
        env:
        - name: COLLECTOR_ZIPKIN_HTTP_PORT
          value: "9411"
        - name: SPAN_STORAGE_TYPE
          value: "elasticsearch"
        - name: ES_SERVER_URLS
          value: "http://opensearch:9200"
        - name: ES_INDEX_PREFIX
          value: "jaeger"
```

## 现有参考环境

[现有环境](http://127.0.0.1:30883/jaeger/ui/search)
 

## docker 部署jeager


[opentelemetry-demo](https://github.com/open-telemetry/opentelemetry-demo)


```shell
docker run -d --name jaeger \
  -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 \
  -p 5775:5775/udp \
  -p 6831:6831/udp \
  -p 6832:6832/udp \
  -p 5778:5778 \
  -p 16686:16686 \
  -p 14268:14268 \
  -p 14250:14250 \
  -p 9411:9411 \
  jaegertracing/all-in-one:latest
```

### 建立jeager和opensearch的关联性

配置Jaeger Collector使用OpenSearch作为存储后端。可以通过环境变量进行配置
```shell
docker run -d --name jaeger \
  -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 \
  -e SPAN_STORAGE_TYPE=elasticsearch \
  -e ES_SERVER_URLS=http://opensearch:9200 \
  -e ES_INDEX_PREFIX=jaeger \
  -p 5775:5775/udp \
  -p 6831:6831/udp \
  -p 6832:6832/udp \
  -p 5778:5778 \
  -p 16686:16686 \
  -p 14268:14268 \
  -p 14250:14250 \
  -p 9411:9411 \
  jaegertracing/all-in-one:latest
```


## TODO k8s 部署 jaeger

jaeger连接opensearch


```yaml

apiVersion: v1
kind: Namespace
metadata:
  name: observability

---
apiVersion: v1
kind: Service
metadata:
  name: jaeger
  namespace: observability
spec:
  ports:
    - port: 5775
      protocol: UDP
      targetPort: 5775
      name: jaeger-agent-compact
    - port: 6831
      protocol: UDP
      targetPort: 6831
      name: jaeger-agent-binary
    - port: 6832
      protocol: UDP
      targetPort: 6832
      name: jaeger-agent-compact
    - port: 5778
      protocol: TCP
      targetPort: 5778
      name: jaeger-agent-config
    - port: 16686
      protocol: TCP
      targetPort: 16686
      name: jaeger-query
    - port: 14268
      protocol: TCP
      targetPort: 14268
      name: jaeger-collector-http
    - port: 14250
      protocol: TCP
      targetPort: 14250
      name: jaeger-collector-grpc
    - port: 9411
      protocol: TCP
      targetPort: 9411
      name: zipkin
  selector:
    app: jaeger

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: observability
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one:latest
          ports:
            - containerPort: 5775
              protocol: UDP
            - containerPort: 6831
              protocol: UDP
            - containerPort: 6832
              protocol: UDP
            - containerPort: 5778
              protocol: TCP
            - containerPort: 16686
              protocol: TCP
            - containerPort: 14268
              protocol: TCP
            - containerPort: 14250
              protocol: TCP
            - containerPort: 9411
              protocol: TCP
          env:
            - name: COLLECTOR_ZIPKIN_HTTP_PORT
              value: "9411"
            - name: SPAN_STORAGE_TYPE
              value: "elasticsearch"
            - name: ES_SERVER_URLS
              value: "http://opensearch:9200"
            - name: ES_INDEX_PREFIX
              value: "jaeger"

```

准备环境
```shell
0 10:57:41 192.168.10.233:~ $ docker pull jaegertracing/all-in-one:latest
latest: Pulling from jaegertracing/all-in-one
661ff4d9561e: Pull complete
cd844493fbde: Pull complete
1f7a10035de3: Pull complete
5895371fe69a: Pull complete
d3fe694c5e30: Pull complete
Digest: sha256:5f70d276f1e9fd05cfbd450bdbf7db6cb26d62f8f314752cd894e259b1fe7165
Status: Downloaded newer image for jaegertracing/all-in-one:latest
docker.io/jaegertracing/all-in-one:latest
0 10:57:56 192.168.10.233:~ $
0 10:58:08 192.168.10.233:~ $
0 10:58:08 192.168.10.233:~ $ docker tag docker.io/jaegertracing/all-in-one:latest dockerhub.kubekey.local/wwmonitor/all-in-one:latest
0 10:58:35 192.168.10.233:~ $ docker push dockerhub.kubekey.local/wwmonitor/all-in-one:latest
The push refers to repository [dockerhub.kubekey.local/wwmonitor/all-in-one]
74aeaf2331bc: Pushed
7487d2c451c3: Pushed
cdf077e82a8d: Pushed
fb47581a5780: Pushed
5af4f8f59b76: Pushed
latest: digest: sha256:033a91b9c572c08e65a0124b3fefc804468c029a0f8439a6b113c848257f449e size: 1366



0 11:00:04 192.168.10.241:~ $ ls -lt | grep opensear
drwxr-xr-x  5 root root      78 5月  16 16:54 opensearch
0 11:00:10 192.168.10.241:~ $ mkdir jaeger
0 11:00:19 192.168.10.241:~ $ cd jaeger
0 11:00:27 192.168.10.241:~/jaeger $ vim jaeger-deployment.yaml

0 11:06:21 192.168.10.241:~/jaeger $ cat jaeger-deployment.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: wwmonitor

---
apiVersion: v1
kind: Service
metadata:
  name: jaeger
  namespace: wwmonitor
spec:
  ports:
    - port: 5775
      protocol: UDP
      targetPort: 5775
      name: jaeger-agent-compact
    - port: 6831
      protocol: UDP
      targetPort: 6831
      name: jaeger-agent-binary
    - port: 6832
      protocol: UDP
      targetPort: 6832
      name: jaeger-agent-compact
    - port: 5778
      protocol: TCP
      targetPort: 5778
      name: jaeger-agent-config
    - port: 16686
      protocol: TCP
      targetPort: 16686
      name: jaeger-query
    - port: 14268
      protocol: TCP
      targetPort: 14268
      name: jaeger-collector-http
    - port: 14250
      protocol: TCP
      targetPort: 14250
      name: jaeger-collector-grpc
    - port: 9411
      protocol: TCP
      targetPort: 9411
      name: zipkin
  selector:
    app: jaeger

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: wwmonitor
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
      - name: jaeger
        image: dockerhub.kubekey.local/wwmonitor/all-in-one:latest
        ports:
        - containerPort: 5775
          protocol: UDP
        - containerPort: 6831
          protocol: UDP
        - containerPort: 6832
          protocol: UDP
        - containerPort: 5778
          protocol: TCP
        - containerPort: 16686
          protocol: TCP
        - containerPort: 14268
          protocol: TCP
        - containerPort: 14250
          protocol: TCP
        - containerPort: 9411
          protocol: TCP
        env:
        - name: COLLECTOR_ZIPKIN_HTTP_PORT
          value: "9411"
        - name: SPAN_STORAGE_TYPE
          value: "elasticsearch"
        - name: ES_SERVER_URLS
          value: "http://opensearch:9200"
        - name: ES_INDEX_PREFIX
          value: "jaeger"
```




## 配置OpenTelemetry SDK以使用Jaeger

安装OpenTelemetry SDK并配置应用程序发送追踪数据到Jaeger。

```go
import (
    "go.opentelemetry.io/otel"
    "go.opentelemetry.io/otel/exporters/jaeger"
    "go.opentelemetry.io/otel/sdk/resource"
    sdktrace "go.opentelemetry.io/otel/sdk/trace"
    "go.opentelemetry.io/otel/trace"
)

func initTracer() (trace.TracerProvider, error) {
    exporter, err := jaeger.New(jaeger.WithCollectorEndpoint(jaeger.WithEndpoint("http://localhost:14268/api/traces")))
    if err != nil {
        return nil, err
    }

    tp := sdktrace.NewTracerProvider(
        sdktrace.WithBatcher(exporter),
        sdktrace.WithResource(resource.NewWithAttributes(
            "service.name", "your-service-name",
        )),
    )
    otel.SetTracerProvider(tp)
    return tp, nil
}
```


## 查看Jaeger UI和OpenSearch Dashboards
- 访问Jaeger UI查看追踪数据：http://localhost:16686。

## TODO 使用教程研究

- 在OpenSearch Dashboards中查看存储的追踪数据。


