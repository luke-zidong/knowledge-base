# 虚拟机连接VPN

# 虚拟机内部挂VPN

参考 https://github.com/wnlen/clash-for-linux

# 虚拟机直连宿主机VPN

## Virtualbox配置Host-only网卡

![image-20250327204253381](C:\Users\lizid\AppData\Roaming\Typora\typora-user-images\image-20250327204253381.png)

在工具里配置Host-Only网卡

![image-20250327204333476](C:\Users\lizid\AppData\Roaming\Typora\typora-user-images\image-20250327204333476.png)

配置当前虚拟机网络，选择Host-Only网卡。

## 宿主机配置VPN

![image-20250327204002608](C:\Users\lizid\AppData\Roaming\Typora\typora-user-images\image-20250327204002608.png)

使用Clash配置VPN，开启**系统代理**和**局域网代理**

## 查询并保存代理ip和port

![image-20250327204549322](C:\Users\lizid\AppData\Roaming\Typora\typora-user-images\image-20250327204549322.png)

ipconfig 查询一下当前使用网卡的ip地址和clash前端界面上的port（一般是7890）

## 虚拟机配置代理地址

vim打开.bashrc配置代理环境变量

```bash
vim ~/.bashrc
```

追加如下命令

```bash
export http_proxy="http://7.247.225.121:7890"
export https_proxy="http://7.247.225.121:7890"
```

# Docker配置代理

虽然terminal配置了代理，但是应用没有配置，若使用代理还需要配置应用的代理，例如常用的Docker

#### **步骤 1：创建代理配置文件**

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo vi /etc/systemd/system/docker.service.d/http-proxy.conf
```

#### **步骤 2：添加代理参数**

```bash
[Service]
Environment="HTTP_PROXY=http://proxy-ip:proxy-port"
Environment="HTTPS_PROXY=http://proxy-ip:proxy-port"
Environment="NO_PROXY=localhost,127.0.0.1"
```

#### **步骤 3：应用配置并重启 Docker**

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```