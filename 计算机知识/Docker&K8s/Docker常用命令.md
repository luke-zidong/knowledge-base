# Docker常用命令

| 命令                 | 用途                          |
| :------------------- | ----------------------------- |
| docker pull          | 获取image                     |
| docker build         | 创建image                     |
| docker images        | 列出image                     |
| docker run           | 运行container                 |
| docker stop [容器ID] | 停止container                 |
| docker ps            | 列出container                 |
| docker rm            | 删除container                 |
| docker rmi           | 删除image                     |
| docker cp            | 在host和container之间拷贝文件 |
| docker commit        | 保存改动为新的image           |

## 注解：

#### docker run 参数

| 命令参数             | 解释                           |
| -------------------- | ------------------------------ |
| -p host端口:容器端口 | 指定映射容器端口到本地host端口 |
| -d                   | 后台运行                       |



