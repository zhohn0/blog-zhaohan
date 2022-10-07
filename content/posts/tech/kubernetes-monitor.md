---
title: "kubernets | 监控" 
date: 2022-10-03
lastmod: 2022-10-03
tags: 
- linux
- centos
- kubernetes
keywords:
- linux
- centos
- kubernetes
- monitor
- metrics
- cadvisor
description: "介绍kubernetes中metric-server监控组件的部署" 
cover:
    image: "https://image.lvbibir.cn/blog/kubernetes.png" 
---
# 前言

基于`centos7.9`，`docker-ce-20.10.18`，`kubelet-1.22.3-0`

cadvisor负责提供数据，已集成到k8s中

Metrics-server负责数据汇总，需额外安装

![Snipaste_2022-10-02_09-04-36](https://image.lvbibir.cn/blog/Snipaste_2022-10-02_09-04-36.png)

# metrics-server部署

下载yaml

```bash
wget https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.2/components.yaml --no-check-certificate
mv components.yaml metric-server.yaml
sed -i 's#apiregistration.k8s.io/v1beta1#apiregistration.k8s.io/v1#' metric-server.yaml
kubectl  apply -f metric-server.yaml
```

修改yaml

```yaml
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.aliyuncs.com/google_containers/metrics-server:v0.5.2
        imagePullPolicy: IfNotPresent
```

**--kubelet-insecure-tls**

不验证kubelet自签的证书

**--kubelet-preferred-address-types=InternalIP**

Metrics-server连接cadvisor默认通过主机名即node的名称进行连接，而Metric-server作为pod运行在集群中默认是无法解析的，所以这里修改成通过节点ip连接