---
layout: post
title: containerd
slug: containerd
author: ymkNK
date: 2024-06-03 14:48:00 +0800
categories: [云原生, docker]
tags: [docker, containerd]
img: 1.jpg
---

## containerd配置

```shell
0 14:34:49 192.168.10.241:/var/lib/containerd $ cat  /etc/containerd/config.toml
version = 2
root = "/var/lib/containerd"
state = "/run/containerd"

[grpc]
  address = "/run/containerd/containerd.sock"
  uid = 0
  gid = 0
  max_recv_message_size = 16777216
  max_send_message_size = 16777216

[ttrpc]
  address = ""
  uid = 0
  gid = 0

[debug]
  address = ""
  uid = 0
  gid = 0
  level = ""

[metrics]
  address = ""
  grpc_histogram = false

[cgroup]
  path = ""

[timeouts]
  "io.containerd.timeout.shim.cleanup" = "5s"
  "io.containerd.timeout.shim.load" = "5s"
  "io.containerd.timeout.shim.shutdown" = "3s"
  "io.containerd.timeout.task.state" = "2s"

[plugins]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
    runtime_type = "io.containerd.runc.v2"
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
      SystemdCgroup = true
  [plugins."io.containerd.grpc.v1.cri"]
    sandbox_image = "dockerhub.kubekey.local/kubesphereio/pause:3.8"
    [plugins."io.containerd.grpc.v1.cri".cni]
      bin_dir = "/opt/cni/bin"
      conf_dir = "/etc/cni/net.d"
      max_conf_num = 1
      conf_template = ""
    [plugins."io.containerd.grpc.v1.cri".registry]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."docker.io"]
          endpoint = ["https://registry-1.docker.io"]
        [plugins."io.containerd.grpc.v1.cri".registry.configs]
          [plugins."io.containerd.grpc.v1.cri".registry.configs."dockerhub.kubekey.local".auth]
            username = "admin"
            password = "Harbor12345"
            [plugins."io.containerd.grpc.v1.cri".registry.configs."dockerhub.kubekey.local".tls]
              ca_file = "/etc/docker/certs.d/dockerhub.kubekey.local/ca.crt"
              cert_file = "/etc/docker/certs.d/dockerhub.kubekey.local/dockerhub.kubekey.local.cert"
              key_file = "/etc/docker/certs.d/dockerhub.kubekey.local/dockerhub.kubekey.local.key"
              insecure_skip_verify = false
```

```shell
0 14:39:30 192.168.10.241:~ $ du -h --max-depth=1 /var/lib/containerd  | sort -hr
19G	/var/lib/containerd
13G	/var/lib/containerd/io.containerd.snapshotter.v1.overlayfs
5.2G	/var/lib/containerd/io.containerd.content.v1.content
12M	/var/lib/containerd/io.containerd.metadata.v1.bolt
2.5M	/var/lib/containerd/io.containerd.grpc.v1.cri
20K	/var/lib/containerd/io.containerd.runtime.v2.task
0	/var/lib/containerd/tmpmounts
0	/var/lib/containerd/io.containerd.snapshotter.v1.native
0	/var/lib/containerd/io.containerd.snapshotter.v1.btrfs
0	/var/lib/containerd/io.containerd.runtime.v1.linux
```

```shell
0 14:39:49 192.168.10.241:~ $ du -h --max-depth=1 /run/containerd  | sort -hr
15G	/run/containerd/io.containerd.runtime.v2.task
15G	/run/containerd
2.7M	/run/containerd/runc
0	/run/containerd/s
0	/run/containerd/io.containerd.runtime.v1.linux
0	/run/containerd/io.containerd.grpc.v1.cri
```

[准确-K8s系列文章-修改containerd 默认数据目录](https://blog.csdn.net/u011197085/article/details/139379836)

```shell
sudo systemctl stop containerd
sudo vim /etc/containerd/config.toml
# 将配置文件中默认的 root 目录从 /var/lib/containerd 修改为 /data/containerd：
sudo mkdir -p /data/containerd

sudo systemctl start containerd
```
> sudo rsync -avzh /var/lib/containerd/ /data/containerd/
{: .prompt-info }


[containerd 迁移数据到指定目录](https://www.cnblogs.com/zbhlinux/p/18034300)
> cp -rp /var/lib/containerd/*  /containerd/data/containerd
{: .prompt-info }

```shell
systemctl daemon-reload && systemctl restart containerd
```



## 定期清理数据

```shell
$ cat  /etc/containerd/config.toml

# 可以有效地控制和管理 /var/lib/containerd 目录的空间使用，防止其无限制增长
[plugins]
  [plugins."io.containerd.snapshotter.v1.overlayfs"]
    prune = "on"
    prune-interval = "24h"
    max-usage = "10G"
```
