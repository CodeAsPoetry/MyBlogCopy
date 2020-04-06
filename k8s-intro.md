---
title: Kubernetes 入门学习笔记
date: 2020-03-29 14:56:20
categories: 工程技术
tags: Kubernetes学习笔记
mathjax: true
---

Kubernetes 基础入门上手，以[Kubernetes基础：开启云原生之门](https://www.imooc.com/learn/978) 作为入手教程，记录学习笔记。

* 应用部署运行模式变迁：从物理单机、虚拟化(容器化)到云原生
* Kubernetes：面向云原生应用的新“云平台”
* Kubernetes 集群初体验<!--more-->
* K8s 架构
* K8s 的基础概念

应用部署运行模式变迁

1. 物理单机(~2000)

2. 虚拟化:
   * 初期(2001～2009) VMWare、Xen、KVM、（Iaas AWS、GCE）
   * 成熟期(2010～至今) OpenStack、PaaS、SaaS

3. 容器化：(2003～至今)
   * Docker：2013年

4. 云原生：初期(2015～至今)
   * 容器技术配合微服务，采用容器管理调度编排引擎CNCF，Kubernetes，Kubernetes 在云原生时代角色大致相当于物理机时代的操作系统、虚拟化模式下的Cloud OS(如 openstack)

![image](/images/K8s_app_mode_change.jpeg)

变迁趋势：应用部署运行更敏捷、更自动化、更有效率、更低成本；开发者更聚焦于应用本身。

Kubernetes 面向云原生应用的新“云平台”，是以Google内部容器编排管理平台Borg为原型的开源实现，一个容器编排管理调度平台、一个微服务支撑平台、一个可移植的“云平台”

功能实现角度——容器编排管理调度平台：

* pod，controllers,configmap,secret等；
* 资源配额与分配管理；
* 健康检查，自愈，伸缩，滚动升级。

微服务支撑平台：

* 服务发现，服务编排，路由支撑
* 快速部署，自动负载均衡
* 对有状态服务的支持

可移植的“云平台”：

* docker将k8s集成到调度引擎等等，k8s成为通用层，新可移植层，新“云平台”
* 为用户提供简单一致的应用部署，伸缩，管理机制；
* 云上应用可以跨云迁移或混用云供应商。

生态圈上，K8s成熟先进，传统云供应商全面支持；云应用上先进的Pod，Controllers管理模式、支持微服务抽象、服务注册，发现和自动负载均衡，K8s都有其强大优势。

Kubernetes：传统云平台上的“Linux”，对应用上云的强大功能支撑

K8s Demo ：

![image](/images/hello_k8s.jpeg)

部署节点设置：

![image](/images/k8s_demo.jpeg)

Service 部署、Deployment 部署、请求的自动负载均衡、服务伸缩、服务版本升级及回退

Kubernetes架构与组件：

Kubernetes架构：

![image](/images/k8s_struct.jpeg)

Master(K8S的Pod集群的控制平面)：

* 所有集群的控制命令都会传递到master组件并执行
* 每个k8s的集群都至少有一套master组件
* 每个master组件都包括api server, scheduler,controller manager三个组件和一个etcd配置中心数据库

组件API Server：

* 集群控制的唯一入口，是提供 Kubernetes 集群控制 RESTful API 的核心组件
* 集群内各个组件之间数据交互和通信的中枢
* 提供集群控制的安全机制(身份认证、授权以及 admission control)

组件Scheduler：

* 通过API Server的Watch接口监听新建Pod副本信息，并通过调度算法为该Pod 选择一个最合适的Node
* 支持自定义调度算法 provider
* 默认调度算法内置预选策略和优选策略，决策考量资源需求、服务质量、软硬件约束、亲缘性、数据局部性等指标参数

组件ControllerManager：

* 集群内各种资源controller的核心管理者

* 针对每一种具体的资源，都有相应的controller

* 保证其下管理的每个Controller所对应的资源始终处于“期望状态”

* ```
  每个Controller的逻辑
  for {
  		获取资源期望状态
  		获取资源当前状态
  		改变：当前状态 -> 期望状态
  }
  ```

组件Etcd：

* Kubernetes 集群的主数据库，存储着所有资源对象以及状态
* 默认与Master组件部署在一个Node上
* Etcd的数据变更都是通过API Server进行

Node组件：

* Kubernetes集群由多个Node共同承担工作负载，Pod被分配到某个具体的Node上执行
* Kubernetes通过node controller对node资源进行管理。支持动态在集群中添加或删除Node
* 每个集群Node上都会部署Kubelet和Kube-proxy两个组件

组件Kubelet：

*  位于集群中每个Node上的非容器形式的服务进程组件，Master和node之间的桥梁
* 处理Master下发到本Node上的Pod创建、启停等管理任务，向API Server注册Node信息
* 监控本Node上容器和节点资源情况，并定期向Master汇报节点资源占用情况

组件Kube-proxy

* Service 抽象概念的实现，将到Service的请求按策略(负载均衡)算法分发到后端Pod(Endpoint)上
* 默认使用iptables mode实现
* 支持nodeport模式，实现从外部访问集群内的service

Kubernetes 对象，一种持久化的、用于表示集群状态的实体

* 一种声明式的意图的记录，一般使用yaml文件描述对象
* Kubernetes集群使用Kubernets对象来表示集群的状态
* 通过API/kubectl管理Kubernetes对象

Kubernetes对象模型：

![image](/images/k8s_object_model.jpeg)

常用Metadata属性：Name和UID、Namespace、Label、Annotations

Pod、Service、Controllers(ReplicaSet、Deployment、StatefulSet、DaemonSet、ConfigMap、Secret)



