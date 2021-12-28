## Docker 安装

>官网文档：[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)
对ubuntu系统

### 卸载旧版本

旧版本的 Docker 称为 `docker` 、`docker.io`, 或者 `docker-engine`，使用以下命令卸载旧版本：

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```
### 支持的存储引擎

Ubuntu 上的 Docker 引擎支持 overlay2、aufs 和 btrfs 存储驱动程序。

Docker 引擎默认使用 overlay2 存储驱动程序。如果需要改用aufs，则需要手动配置

### 安装方法

可以根据需要以不同方式安装 Docker Engine：

* 【推荐】使用apt进行安装
* 下载 DEB 包，手动安装并完全手动管理升级。这在某些情况下非常有用，例如在无法访问互联网的内网系统上安装 Docker。
* 在测试和开发环境中，部分用户选择使用自动化脚本来安装Docker。
此处以apt方式进行安装，其他方法请参考官网文档

#### 使用 APT 安装docker

 由于 `apt` 源使用 HTTPS 以确保软件下载过程中不被篡改。因此，我们首先需要添加使用 HTTPS 传输的软件包以及 CA 证书。

```shell
$ sudo apt-get update
$ sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
鉴于国内网络问题，强烈建议使用国内源，官方源请在注释中查看。
为了确认所下载软件包的合法性，需要添加软件源的 `GPG` 密钥。

```shell
 # 阿里云源
 $ curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

 # 官网源：curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

然后，我们需要向 `sources.list` 中添加 Docker 软件源 

```shell
# 阿里云源
$ echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 官方源
$  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
最后安装docker
```shell
 $ sudo apt-get update
 $ sudo apt-get install docker-ce docker-ce-cli containerd.io
```
#### 安装指定版本的docker 

如果需要安装指定版本的docker

1) 显示repo中可用的docker版本

```shell
 $ apt-cache madison docker-ce
```
2) 进行安装
```shell
 sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
```
### 验证Docker是否正确安装

通过运行 hello-world 镜像验证 Docker Engine 是否已正确安装。

```shell
 docker run hello-world
```

