# Dockerfile语法

| 命令       | 用途                                                         |
| ---------- | ------------------------------------------------------------ |
| FROM       | 指定base image                                               |
| MAINTAINER | Author，对作者的简单描述，自定义。                           |
| COPY       | 将文件或目录从 build context 复制到镜像，其支持两种格式：COPY src dest 和 COPY[“src”,“dest”] |
| ADD        | 与 COPY 类似，复制文件到镜像，不同的是，ADD 的 src 是归档文件（tar、zip、tgz 等），这些归档文件会被**自动解压**到 dest （镜像目标路径），无需手动解压。 |
| ENV        | 设置环境变量，该变量可被后面的指令使用。                     |
| EXPOSE     | 指定容器中的进程会监听的某个端口，指定后 Docker 可以将该端口暴露出来。 |
| VOLUME     | 将文件或目录声明为 volume（挂载），同样 Docker 可以将该目录或文件映射出来。 |
| WORKDIR    | 为后面的 RUN、CMD、ENTRYPOINT、ADD、COPY 指令设置镜像中的当前工作目录。 |
| RUN        | 在容器中运行指令的命令。                                     |
| CMD        | 启动容器时运行指定的命令，Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，如果 docker run 后面指定有参数，该参数将会替换 CMD 的参数。 |
| ENTRYPOINT | 同样，在 Dockerfile 中可以有多个 ENTRYPOINT 指令，也是只有最后一个生效，但与 CMD 不同的是，CMD 或 docker run 之后的参数会被当作参数传给 ENTRYPOINT。 |
| USER       | 指定用户                                                     |

#  RUN、CMD 和 ENTRYPOINT 指令的异同

- RUN、CMD 和 ENTRYPOINT 指令都可以用来执行具体的命令.

- RUN 指令是在 Docker **镜像构建时**发挥作用, 可以使用多个该命令, 且执行结果会记录到镜像中.
- CMD 和 ENTYPOINT 指令是在**容器启动时自动执行**, 均只有最后一个该指令有效, 且均可以在 docker run 中被覆盖.
- ENTRYPOINT 指令和 CMD 的区别在于使用 ENTRYPOINT 时 CMD 指令会被作为其默认参数, 而用户也可以在启动容器时通过覆盖 CMD 指令来输入参数; 此外, 这也意味着 ENTRYPOINT 指令的内容不**易被用户命令覆盖**.

#  ADD 和 COPY 的区别和使用场景

- ADD 支持添加远程 url 和自动提取压缩格式的文件，COPY 只允许从本机中复制文件
- COPY 支持从其他构建阶段中复制源文件(--from)
- 根据官方 Dockerfile 最佳实践，除非真的需要从远程 url 添加文件或自动提取压缩文件才用 ADD，其他情况一律使用 COPY

**注意**
- ADD 从远程 url 获取文件和复制的效果并不理想，因为该文件会增加 Docker Image 最终的大小
- 相反，应该使用 curl huo wget 来获取远程文件，然后在不需要它时进行删除

# 例子

```dockerfile
FROM ubuntu		# base image 是 ubuntu
MAINTAINER xbf	# 指定维护者，一般写作者名字
RUN apt-get update	
RUN apt-get install -y nginx
COPY index.html /var/www/html
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
EXPOSE 80
```

# 参考

[Docker 中 RUN、CMD 与 ENTRYPOINT 的区别](https://blog.csdn.net/IT_ZRS/article/details/124707747)

[浅析 Dockerfile 中 RUN、CMD 以及 ENTRYPOINT 指令的异同](https://blog.csdn.net/LostUnravel/article/details/125819584)
