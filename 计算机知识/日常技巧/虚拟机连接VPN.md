# 虚拟机连接VPN

# 虚拟机内部挂VPN

参考 https://github.com/wnlen/clash-for-linux

# 虚拟机直连宿主机VPN

## Virtualbox配置Host-only网卡

![VBOX网卡.png](https://github.com/luke-zidong/knowledge-base/blob/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/%E6%97%A5%E5%B8%B8%E6%8A%80%E5%B7%A7/images/VBOX%E7%BD%91%E5%8D%A1.png?raw=true)

在工具里配置Host-Only网卡

![VBOX虚拟机配置.png](https://github.com/luke-zidong/knowledge-base/blob/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/%E6%97%A5%E5%B8%B8%E6%8A%80%E5%B7%A7/images/VBOX%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%85%8D%E7%BD%AE.png?raw=true)

配置当前虚拟机网络，选择Host-Only网卡。

## 宿主机配置VPN

![clash配置.png](https://github.com/luke-zidong/knowledge-base/blob/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/%E6%97%A5%E5%B8%B8%E6%8A%80%E5%B7%A7/images/clash%E9%85%8D%E7%BD%AE.png?raw=true)

使用Clash配置VPN，开启**系统代理**和**局域网代理**

## 查询并保存代理ip和port

![ifconfig.png](https://github.com/luke-zidong/knowledge-base/blob/main/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%9F%A5%E8%AF%86/%E6%97%A5%E5%B8%B8%E6%8A%80%E5%B7%A7/images/ifconfig.png?raw=true)

ipconfig 查询一下当前使用网卡的ip地址和clash前端界面上的port（一般是7890）

## 虚拟机配置代理地址

vim打开.bashrc配置代理环境变量

```bash
vim ~/.bashrc
```

追加如下命令

```bash
export http_proxy=http://7.247.225.121:7890
export https_proxy=http://7.247.225.121:7890
export no_proxy=127.0.0.1,localhost
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
