# 概念梳理

## VPS

云厂商一般会**将一台物理服务器分割成多个虚拟机**。它跟我们在 windows 用 `VMware`, `VirtualBox` 建的虚拟机其实是一回事。每个虚拟机都拥有独立的操作系统、资源（比如 CPU、内存、存储空间）和公网 IP 地址。然后对外出售，这样的虚拟机就是所谓的 **VPS**（Virtual Private Server，虚拟专用服务器）

## ECS

传统 VPS 有个缺点，不支持用户**自主升降级**，它的资源是预先分配的，不易动态调整。举个例子，假设你买了 `1c1g` 的服务器，想在页面上点点两下升级成 `2c2g`，这在传统 VPS 里是不支持的。如果给 VPS 加入自主升降级的功能，那它就成了 **ECS**（Elastic Compute Service，弹性计算服务）

## Docker

只打包**软件和系统依赖库加配置**，然后将这部分系统文件挂到 ecs 的操作系统下，利用一个叫 **Namespace** 的能力让它看起来就像是一个独立操作系统一样。再利用一个叫 **Cgroup** 的能力限制它能使用的计算资源。这就省掉了一层笨重的操作系统，同时还让软件轻松跑在各类操作系统上。这就是我们常说的 **Docker 容器技术**。**Docker** 本质上就是一个将**程序和环境打包并运行**的工具软件。

![图片](https://raw.githubusercontent.com/luke-zidong/knowledge-base/refs/heads/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/Docker%26K8s/image/Docker%E7%AE%80%E4%BB%8B.webp)

## 基础镜像

选中一个基础操作系统和语言后，我们将它们对应的文件系统，依赖库，配置等放一起打包成一个类似压缩包的文件，这就是所谓的**基础镜像**(Base Image)。

## Dockerfile

基于一个装了 python3.9 解释器的操作系统（基础镜像），再执行 pip install 等命令安装其他依赖，从而构建出一个适合程序运行的环境，最后用 `python app.py` 运行我们的目标应用程序。像这样一份列清楚了，从操作系统到应用服务启动，需要做哪些事情的清单文件（todo list），就是所谓的 **Dockerfile**。

## 容器镜像

注意 Dockerfile 只是描述了要做哪些事情，并没有真正开始做。当我们用命令行执行 **docker build** 的时候，Docker 软件就会按着 Dockerfile 的说明，一行行构建环境+应用程序。最终将这个环境+程序，打包成一个类似"压缩包"的东西，我们叫它**容器镜像**(container image)。

## Registry

参考 `github` 代码仓库 的做法，我们通常会使用 `git push` 将代码传到 github，有需要的人自己通过 `git pull` 的方式将代码从 github 拉到自己的机器上。那 Docker 也一样，弄一个**镜像仓库**，通过 **docker push** 将镜像推到仓库，有需要的时候再通过 **docker pull** 将镜像拉到机器上。这个负责管理镜像仓库推拉能力的服务，就叫 **Docker Registry**。

## 容器

执行 docker run 命令，将这个类似"压缩包"的容器镜像给"解压缩"，获得一个**独立的环境和应用程序**并运行起来。这样一个独立的环境和应用程序，就是所谓的**容器**(container)。

## Docker 和虚拟机的关系？

传统虚拟机自带一个完整操作系统，而容器本身不带完整操作系统，容器的基础镜像实际上只包含了操作系统的核心依赖库和配置文件等必要组件。它利用一个叫 **Namespace** 的能力让它看起来就像是一个独立操作系统一样。再利用一个叫 **Cgroup** 的能力限制它能使用的计算资源。

![图片](https://raw.githubusercontent.com/luke-zidong/knowledge-base/refs/heads/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/Docker%26K8s/image/Docker%E5%92%8C%E8%99%9A%E6%8B%9F%E6%9C%BA.webp)

所以说，容器本质上只是个自带独立运行环境的**特殊进程**，底层用的其实是**宿主机的操作系统内核**。

## Docker 的架构原理

Docker 的架构原理是经典的 Client/Server 架构。Client 对应 Docker-cli， Server 对应 Docker daemon。我们在命令行里敲 Docker 命令，使用的就是 Docker-cli.

![Docker架构简图](https://raw.githubusercontent.com/luke-zidong/knowledge-base/refs/heads/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/Docker%26K8s/image/Docker%E6%9E%B6%E6%9E%84%E7%AE%80%E5%9B%BE.webp)

Docker-cli 会解析我们输入的 cmd 命令，然后调用 Docker daemon 守护进程提供的 RESTful API，守护进程收到命令后，会根据指令创建和管理各个容器。再具体点，Docker Daemon 内部分为 Docker Server、Engine 两层。Docker Server 本质上就是个 HTTP 服务，负责对外提供操作容器和镜像的 api 接口，接收到 API 请求后，会分发任务给 Engine 层，Engine 层负责创建 Job，由 Job 实际执行各种工作。

![Docker架构简图](https://raw.githubusercontent.com/luke-zidong/knowledge-base/refs/heads/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/Docker%26K8s/image/Docker%E5%90%8E%E5%8F%B0.webp)

不同的 Docker 命令会执行不同类型的 Job 任务。

**整体架构如下：**

![Docker架构](https://github.com/luke-zidong/knowledge-base/blob/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/Docker&K8s/image/Docker%E6%9E%B6%E6%9E%84.jpg?raw=true)

## Docker Compose

Docker 容器 本身只是**一个**特殊进程，但如果我想要部署**多个**容器，且对这些容器的顺序有一定要求呢？比如一个博客系统，当然是先启动数据库，再启动身份验证服务，最后才能启动博客 web 服务。按理说挨个执行 docker run 命令当然是没问题的，但有没有更优雅的解决方案？有。我们可以通过一个 **YAML** 文件写清楚要部署的**容器有哪些**，**部署顺序**是怎么样的，以及这些容器占用的 **cpu 和内存**等信息。

```yaml
version: "3.8"

services:
  A:
    image: "some-image-for-a"
    deploy:
      resources:
        limits:
          cpus: "0.50" # 限制 CPU 使用率为 50%
          memory: 256M # 限制内存使用量为 256MB

  B:
    image: "some-image-for-b"
    depends_on:
      - A

  C:
    image: "some-image-for-c"
    depends_on:
      - B
```

然后，通过一行`Docker-compose up`命令，开始解析 YAML 文件，将容器们一键按顺序部署，就完成**一整套服务**的部署。这其实就是 **Docker Compose** 干的事情。

## Docker Swarm

Docker 解决的是**一个容器**的部署。Docker Compose 解决的是**多个容器组成的一整套服务**的部署。那 Docker Swarm 就更高维度了，它解决的其实是这一整套服务**在多台服务器上的集群部署**问题。比如在 A 服务器坏了，就将服务在 B 服务器上重新部署一套，实现迁移，还能根据需要对服务做扩缩容。

## Docker 和 k8s 的关系

其实 Docker Swarm 是 k8s 的**竞品**，既然是竞品，那它们做的事情其实区别就不大了。现在回过头来看 Docker 容器和 k8s 之间的关系，思路就清晰了。Docker 部署的**容器**，其实就是 k8s 调度的 Pod 里的 **container**，它们都叫**容器**，其实是一回事。只不过 k8s 除了支持 Docker 的容器外，还支持别人家的容器。Docker Compose 基于多个 container 创建的**一整套服务**，其实就是 k8s 里的 **pod**。而 Docker Swarm 做的事情和 k8s 一样，本质上就是在调度 pod。回过头来看下 k8s 的官方定义，叫容器编排引擎，将它理解为，以 API **编**程的方式管理安**排**各个容器的引擎，是不是就特别精辟。

# 总结

- Docker 本质上就是一个将**程序和环境打包并运行**的工具软件，而 Docker 容器本质上只是个自带独立运行环境的**特殊进程**，底层用的其实是**宿主机的操作系统内核**。
- Docker 软件 通过 Dockerfile 描述环境和应用程序的依赖关系， docker build 构建镜像， docker pull/push 跟 Docker Registry 交互实现存储和分发镜像，docker run 命令基于镜像启动容器，基于容器技术运行程序和它对应的环境，从而解决环境依赖导致的各种问题。
- Docker 解决的是**一个容器**的部署问题，Docker Compose 解决的是**多个容器组成的一套服务**的部署问题，Docker Swarm 解决的是多个容器组成的**一套服务在多台服务器上的部署问题**，k8s 则是 Docker Swarm 的竞品，在更高维度上**兼容**了 Docker 容器，实现了容器编排调度。

## 相关学习文章

[Docker和传统虚拟机有什么区别](https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247534049&idx=2&sn=1ef2674ddb3217bbafcb5cd6946407ac&chksm=f98d014bcefa885d6b68c0405718abf634a33427264a8ae4d04bc478bc03121dbfdceed7012e&token=630123097&lang=zh_CN#rd)

[Docker 和 k8s 之间是什么关系](https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247534155&idx=2&sn=d9a02a778656b968340f4ea0993d57e5&chksm=f98d02e1cefa8bf761e19bd337678c31bf9aa62eb4d668b3358d9ab3ac9300ca8023a26a03d1&token=630123097&lang=zh_CN#rd)

[Docker入门](https://www.imooc.com/learn/867)

[Docker 从入门到实践](https://vuepress.mirror.docker-practice.com/)

[由浅入深吃透 Docker-完](https://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/%E7%94%B1%E6%B5%85%E5%85%A5%E6%B7%B1%E5%90%83%E9%80%8F%20Docker-%E5%AE%8C)