# Kubernetes安装手册 - 1 组件选择

## 前言
后kubernetes时代， kubernetes已成为最流行的容器编排平台，2019年开始学习kubernetes还不晚，本文将带你一步一步建立一个高性能，高可用的，可用于生产的kubernetes集群

## 方法
### 二进制 安装
二进制安装是传统的安装方式，大多数互联网公司通过Ansible, Salt Stack等自动化工具部署集群。本文采用二进制安装，让自己更加熟悉kubernetes各组件的用途，以及组件之间的关系

### kubeadm 安装
随着kubernetes 1.13的发布， kubeadm已经正是GA， 现在可以用kubeadm创建高可用集群了，随着kubernetes发展，相信以后用kubeadm搭建集群应该是个更好的选择。

## 组件选择

### 核心组件
- 容器编排平台 Kubernetes 1.13.3
- 高性能，高可用Key-Value数据库 Etcd 3.3.11
- 容器运行时 Docker-ce 18.09.1
- 网络CNI插件 Calico 3.5.0

### 插件
- 域名解析 Coredns
- 仪表盘 Dashboard
- 监控组件 Prometheus, Metrics-Server
- 存储 Rook
- Ingress代理 Traefik
- 日志搜集 EFK (elasticsearch、fluentd、kibana)

### 镜像仓库
- Harbor

## 配置各组件
### etcd
- 至少三台服务器的etcd高可用集群，和kubernetes分离

### kube-apiserver
- 使用 VIP (keepalived 和 haproxy) 实现高可用
- 关闭非安全端口 8080 和匿名访问
- 在安全端口 6443 接收 https 请求
- 严格的认证和授权策略 (x509、token、RBAC)
- 开启 bootstrap token 认证，支持 kubelet TLS bootstrapping
- 使用 https 访问 kubelet、etcd，加密通信

### kube-controller-manager
- 高可用
- 关闭非安全端口，在安全端口 10252 接收 https 请求
- 使用 kubeconfig 访问 apiserver 的安全端口
- 自动 approve kubelet 证书签名请求 (CSR)，证书过期后自动轮转
- 各 controller 使用自己的 ServiceAccount 访问 apiserver

### kube-scheduler
- 高可用
- 使用 kubeconfig 访问 apiserver 的安全端口

### kubelet
- 使用 kubeconfig 访问 apiserver 的安全端口
- 使用 TLS bootstrap 机制自动生成 client 和 server 证书，过期后自动轮转
- 使用JSON文件配置主要参数
- 关闭只读端口，在安全端口 10250 接收 https 请求，对请求进行认证和授权，拒绝匿名访问和非授权访问

### kube-proxy
- 使用 kubeconfig 访问 apiserver 的安全端口
- 使用YAML文件配置主要参数
- 使用 ipvs 代理模式

### DNS
- 使用性能更好的 coredns

### Dashboard
- 支持登录认证

### Metric
- Prometheus、metrics-server

### 日志
- Elasticsearch、Fluend、Kibana

### 镜像库
- 有条件的话，用单独一台机器部署Harbor

<style type="text/css">
h3 {color: #3194d0;}
</style>