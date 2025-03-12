# 概念梳理

## VPS

云厂商一般会**将一台物理服务器分割成多个虚拟机**。它跟我们在 windows 用 `VMware`, `VirtualBox` 建的虚拟机其实是一回事。每个虚拟机都拥有独立的操作系统、资源（比如 CPU、内存、存储空间）和公网 IP 地址。然后对外出售，这样的虚拟机就是所谓的 **VPS**（Virtual Private Server，虚拟专用服务器）

## ECS

传统 VPS 有个缺点，不支持用户**自主升降级**，它的资源是预先分配的，不易动态调整。举个例子，假设你买了 `1c1g` 的服务器，想在页面上点点两下升级成 `2c2g`，这在传统 VPS 里是不支持的。如果给 VPS 加入自主升降级的功能，那它就成了 **ECS**（Elastic Compute Service，弹性计算服务）

## Docker

只打包**软件和系统依赖库加配置**，然后将这部分系统文件挂到 ecs 的操作系统下，利用一个叫 **Namespace** 的能力让它看起来就像是一个独立操作系统一样。再利用一个叫 **Cgroup** 的能力限制它能使用的计算资源。这就省掉了一层笨重的操作系统，同时还让软件轻松跑在各类操作系统上。这就是我们常说的 **Docker 容器技术**。

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



## 参考

[Docker和传统虚拟机有什么区别](https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247534049&idx=2&sn=1ef2674ddb3217bbafcb5cd6946407ac&chksm=f98d014bcefa885d6b68c0405718abf634a33427264a8ae4d04bc478bc03121dbfdceed7012e&token=630123097&lang=zh_CN#rd)

[Docker 和 k8s 之间是什么关系](https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247534155&idx=2&sn=d9a02a778656b968340f4ea0993d57e5&chksm=f98d02e1cefa8bf761e19bd337678c31bf9aa62eb4d668b3358d9ab3ac9300ca8023a26a03d1&token=630123097&lang=zh_CN#rd)

[Docker入门](https://www.imooc.com/learn/867)