# 什么是Kubernetes

Kubernetes就是一个**生产级别的容器编排平台和集群管理系统**，不仅能够创建、调度容器，还能够监控、管理服务器，它凝聚了Google等大公司和开源社区的集体智慧，从而让中小型公司也可以具备轻松运维海量计算节点——也就是“云计算”的能力。

从某种角度来看，Kubernetes可以说是一个集群级别的操作系统，主要功能就是资源管理和作业调度。但Kubernetes不是运行在单机上管理单台计算资源和进程，而是运行在多台服务器上管理几百几千台的计算资源，以及在这些资源上运行的上万上百万的进程，规模要大得多。

# Kubernetes的基本架构

![图片](D:\githubRepository\knowledge-base\计算机知识\Docker&K8s\image\k8s架构.png)

Kubernetes采用了现今流行的“**控制面/数据面**”（Control Plane / Data Plane）架构，集群里的计算机被称为“**节点**”（Node），可以是实机也可以是虚机。

**控制面**的节点在Kubernetes里叫做**Master Node**，一般简称为**Master**，它是整个集群里最重要的部分，可以说是Kubernetes的大脑和心脏。

**数据面**的节点叫做**Worker Node**，一般就简称为**Worker**或者**Node**，相当于Kubernetes的手和脚，在Master的指挥下干活。

# 节点内部的结构

Kubernetes的节点内部也具有复杂的结构，是由很多的模块构成的，这些模块又可以分成**组件（Component）**和**插件（Addon）**两类。

**组件**实现了Kubernetes的核心功能特性。

**插件**则是Kubernetes的一些附加功能，属于“锦上添花”，不安装也不会影响Kubernetes的正常运行。

## Master的组件

Master里有4个组件，分别是**apiserver**、**etcd**、**scheduler**、**controller-manager**。

![图片](D:\githubRepository\knowledge-base\计算机知识\Docker&K8s\image\K8sMaster.png)

**apiserver**是Master节点——同时也是整个Kubernetes系统的**唯一入口**，它对外公开了一系列的RESTful API，并且加上了验证、授权等功能，所有其他组件都只能和它直接通信，可以说是Kubernetes里的联络员。

**etcd**是一个高可用的分布式Key-Value数据库，用来**持久化存储**系统里的各种资源对象和状态，相当于Kubernetes里的配置管理员。注意它只与apiserver有直接联系，也就是说任何其他组件想要读写etcd里的数据都必须经过apiserver。

**scheduler**负责容器的**编排工作**，检查节点的资源状态，把Pod调度到最适合的节点上运行，相当于部署人员。因为节点状态和Pod信息都存储在etcd里，所以**scheduler必须通过apiserver才能获得**。

**controller-manager**负责**维护容器和节点等资源的状态**，实现故障检测、服务迁移、应用伸缩等功能，相当于监控运维人员。同样地，它也必须通过apiserver获得存储在etcd里的信息，才能够实现对资源的各种操作。

## Node的组件

Node里的3个组件分别是**kubelet**、**kube-proxy**、**container-runtime**

![图片](D:\githubRepository\knowledge-base\计算机知识\Docker&K8s\image\k8sNode.png)

**kubelet**是Node的代理，负责管理Node相关的绝大部分操作，Node上只有它能够与apiserver通信，实现状态报告、命令下发、启停容器等功能，相当于是Node上的一个“小管家”。

**kube-proxy**的作用有点特别，它是Node的网络代理，只负责管理容器的网络通信，简单来说就是为Pod转发TCP/UDP数据包，相当于是专职的“小邮差”。

**container-runtime**是容器和镜像的实际使用者，在kubelet的指挥下创建容器，管理Pod的生命周期，是真正干活的“苦力”。

## Kubernetes工作流程

把Node里的组件和Master里的组件放在一起来看，就能够明白Kubernetes的大致工作流程了：

- 每个Node上的**kubelet**会定期向**apiserver**上报节点状态，**apiserver**再存到**etcd**里。
- 每个Node上的**kube-proxy**实现了TCP/UDP反向代理，让容器对外提供稳定的服务。
- **scheduler**通过**apiserver**得到当前的节点状态，调度Pod，然后**apiserver**下发命令给某个Node的**kubelet**，**kubelet**调用**container-runtime**启动容器。
- **controller-manager**也通过**apiserver**得到实时的节点状态，监控可能的异常情况，再使用相应的手段去调节恢复。

## 插件（Addons）

只要服务器节点上运行了apiserver、scheduler、kubelet、kube-proxy、container-runtime等组件，就可以说是一个功能齐全的Kubernetes集群了。

不过就像Linux一样，操作系统提供的基础功能虽然“可用”，但想达到“好用”的程度，还是要再安装一些附加功能，这在Kubernetes里就是**插件（Addon）**。

由于Kubernetes本身的设计非常灵活，所以就有大量的插件用来扩展、增强它对应用和集群的管理能力。

插件中我个人认为比较重要的有两个：**DNS**和**Dashboard**。

**DNS**在Kubernetes集群里实现了域名解析服务，能够让我们以域名而不是IP地址的方式来互相通信，是服务发现和负载均衡的基础。由于它对微服务、服务网格等架构至关重要，所以基本上是Kubernetes的必备插件。

**Dashboard**就是仪表盘，为Kubernetes提供了一个图形化的操作界面，非常直观友好，虽然大多数Kubernetes工作都是使用命令行kubectl，但有的时候在Dashboard上查看信息也是挺方便的。

# 参考

[Kubernetes入门实战课](https://learn.lianglianglee.com/%e4%b8%93%e6%a0%8f/Kubernetes%e5%85%a5%e9%97%a8%e5%ae%9e%e6%88%98%e8%af%be/09%20%e8%b5%b0%e8%bf%91%e4%ba%91%e5%8e%9f%e7%94%9f%ef%bc%9a%e5%a6%82%e4%bd%95%e5%9c%a8%e6%9c%ac%e6%9c%ba%e6%90%ad%e5%bb%ba%e5%b0%8f%e5%b7%a7%e5%ae%8c%e5%a4%87%e7%9a%84Kubernetes%e7%8e%af%e5%a2%83.md)