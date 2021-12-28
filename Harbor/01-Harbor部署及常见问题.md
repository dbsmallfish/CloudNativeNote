## Harbor 部署

官网文档：[https://goharbor.io/docs/2.3.0/](https://goharbor.io/docs/2.3.0/)

OSS: [https://github.com/docker/docker.github.io/blob/master/registry/storage-drivers/oss.md](https://github.com/docker/docker.github.io/blob/master/registry/storage-drivers/oss.md)

### 前置环境

#### 1、安装docker

```shell
$ curl -fsSL http://mirrors.cloud.aliyuncs.com/docker-ce/linux/ubuntu/gpg |  sudo apt-key add -
# 在/etc/apt/source.list新增以下行
$ vim /etc/apt/source.list
deb [arch=amd64] http://mirrors.cloud.aliyuncs.com/docker-ce/linux/ubuntu focal stable
$ apt-get update
$ apt-get -y install docker-ce=5:19.03.14~3-0~ubuntu-focal docker-ce-cli=5:19.03.14~3-0~ubuntu-focal containerd.io
```
#### 2、安装docker-compose

```shell
# 下载 docker compose
$ curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
$ ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
### 部署harbor

>Harbor的安装包分为在线版和离线版，离线版包含安装所需的所有镜像
>Harbor无法登录问题： [https://blog.csdn.net/weixin_43952432/article/details/100577560](https://blog.csdn.net/weixin_43952432/article/details/100577560) 
>
>####  下载harbor离线安装包

```shell
$ wget https://github.com/goharbor/harbor/releases/download/v2.1.0/harbor-offline-installer-v2.1.0.tgz
# 上传到服务器后,解压压缩包
$ tar -xf harbor-offline-installer-v2.1.0.tgz
```
#### 修改harbor配置

进到harbor-offline-installer-v2.1.0.tgz 解压后的目录, 从harbor.yml.tmpl复制一份harbor.yml

```yaml
$ cp harbor.yml.tmpl harbor.yml
$ vim harbor.yml
# Configuration file of Harbor
# 此处定义为harbor的域名或者IP，注意不能定义为 localhost、127.0.0.1
hostname: harbor.domin.com
# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 80
# 这里把https注释掉（根据自己实际需求配置）
# https:
  # https port for harbor, default is 443
  # port: 443
  # The path of cert and key files for nginx
  # certificate: /your/certificate/path
  # private_key: /your/private/key/path
  
# 如果要启用外部代理，请取消去掉external_url的注释，当它启用时，hostname将不再使用
# 因为我们还会给harbor接入反向代理，所以此处去掉external_url的注释
external_url: https://harbor.domin.com 
​
# harbor admin账户的初始密码（建议修改）
harbor_admin_password: Harbor12345
​
# 因为我们会使用外部postgres数据库，所以此处此database的配置可以忽略
database:
  password: root123
  max_idle_conns: 50
  max_open_conns: 1000
​
# 默认harbor数据存储目录
# 注意：虽然本次部署会使用oss作为registry的外部存储，但是data_volume字段不要注释掉
data_volume: /data/harbor-storage 
​
# 更改镜像存储位置，本地的话不用配置直接用，本次部署中使用了阿里云的oss作为后端存储
# 去掉storage注释，配置oss 可参考：https://docs.docker.com/registry/configuration/#storage
​
storage:
  cache:
    layerinfo: redis
  oss:
    accesskeyid: 填写你的具有阿里云oss权限账户的RAM的AccessKey ID
    accesskeysecret: 填写你的具有阿里云oss权限账户的RAM的AccessKey Secret
    region: 地域节点 # EndPoint, 如cn-shenzhen
    endpoint: Bucket 域名 #[bucket].[region].aliyuncs.com 或者 当 internal=true， [bucket].[region]-internal.aliyuncs.com
    internal: 
    secure: true   # 指定是否通过ssl传输数据到bucket
    bucket: Bucket 名称  
    rootdirectory: 指定oss下面某路径作为存储目录
​
# 保持默认配置
trivy:
  ignore_unfixed: false
  skip_update: false
  insecure: false
# 保持默认配置
jobservice:
  max_job_workers: 10
# 保持默认配置
notification:
  # Maximum retry count for webhook job
  webhook_job_max_retry: 10
# 保持默认配置
chart:
  absolute_url: disabled
# 日志配置，根据自己需求进行修改
log:
  level: info
  local:
    rotate_count: 50
    rotate_size: 200M
    location: /var/log/harbor
_version: 2.2.0
​
# 使用外部数据库，需要把external_database的注释取消，进行配置， 注意需要在postgres数据库中将registry、clair的数据库预先创建
 external_database:
   harbor:
     host: harbor_db_host
     port: harbor_db_port
     db_name: harbor_db_name
     username: harbor_db_username
     password: harbor_db_password
     ssl_mode: disable
     max_idle_conns: 2
     max_open_conns: 0
  clair:
     host: clair_server_db_host
     port: clair_server_db_port
     db_name: clair_server_db_name
     username: clair_server_db_username
     password: clair_server_db_password
     ssl_mode: disable     
#   notary_signer:
#     host: notary_signer_db_host
#     port: notary_signer_db_port
#     db_name: notary_signer_db_name
#     username: notary_signer_db_username
#     password: notary_signer_db_password
#     ssl_mode: disable
#   notary_server:
#     host: notary_server_db_host
#     port: notary_server_db_port
#     db_name: notary_server_db_name
#     username: notary_server_db_username
#     password: notary_server_db_password
#     ssl_mode: disable
​
# 如果使用外部redis的情况下，需要将external_redis的注释去掉
 external_redis:
   host: redis:6379
   password:
   registry_db_index: 1
   jobservice_db_index: 2
   chartmuseum_db_index: 3
   trivy_db_index: 5
   idle_timeout_seconds: 30
```
#### 安装harbor

1. 使用prepare初始化harbor配置文件
```plain
./prepare --with-clair --with-trivy  --with-chartmuseum
#--with-clair  镜像安全扫描插件
#--with_notary 内容信任插件 
#--with-trivy   镜像漏洞检测插件
#--with-chartmuseum Chart仓库服务
```
2. 注释掉docker-compose.yml 中registry、registryctl、chartmuseum 的本地存储路径，使用oss进行存储
>确认 common/config/registry/config.yaml 、common/config/chartserver/env配置的是否是OSS
```yaml
# 如：以registry为例
registry:
    image: goharbor/registry-photon:v2.0.0
    container_name: registry
    restart: always
    cap_drop:
      - ALL
    cap_add:
      - CHOWN
      - SETGID
      - SETUID
    volumes:
#      - /data/registry:/storage:z
```
3. 如果harbor 前面还会加一层proxy代理，且代理中有配置“X-Forwarded-Proto”，则需要将harbor的proxy组件中的配置文件’`X-Forwarded-Proto`‘注释或删掉
```plain
sed -i '/X-Forwarded-Proto/d' common/config/nginx/nginx.conf
```
4. 启动Harbor
```plain
# 将harbor相关镜像文件导入
docker load -i harbor.v2.1.0.tar.gz
# 启动harbor
docker-compose up -d 
```
#### 验证harbor

增加harbor的域名到docker-daemon

```shell
$ vim /etc/docker/daemon.json 
{
    "insecure-registries": ["harbor.djicorp.com"]
}
# 重载docker服务
$ systemctl reload docker
```
绑定hosts
```shell
$ vim /etc/hosts
[ip] harbor.djicorp.com
```
### harbor常见问题

#### **harbor push出现unknow blob**

**解决**：一般出现这个问题是由：X-Forwarded-Proto 这个导致的，如果harbor应用前有多个nginx 代理，要确保只保留一个

```plain
# 删除harbor自己nginx配置中的X-Forwarded-Proto
sed -i '/X-Forwarded-Proto/d' common/config/nginx/nginx.conf
```
#### **阿里云harbor实例前的slb健康检测不通过**

查看slb的健康检查日志提示以下问题：

```plain
[10.225.12.214]:80 to 10.225.6.203:80 abnormal; cause: check protocol http error
```
**排查**
>slb 健康检查异常报错：[https://www.sites-help.com/aliyun/1575843803.html](https://www.sites-help.com/aliyun/1575843803.html)
check protocol http error 表示响应中HTTP status code不是用户指定的(默认为 2xx/3xx)

```plain
1、在harbor应用服务器上使用NC检测，发现结果返回的状态码确实为400
echo -e 'HEAD /harbor/sign-in HTTP/1.0\r\n' | nc -t 10.225.6.203 80
​
2、增加Host后，结果返回的状态码为200
echo -e ‘HEAD /harbor/sign-in HTTP/1.0\r\nHost: harbor.djicorp.com\r\n\r\n’ | nc -t 10.225.6.203 80
```
**解决**
将阿里云slb的健康检查中增加”健康检查域名： hc-harbor.djicorp.com“

#### **harbor 登录状态时好时坏**

**排查**：harbor后端有两个实例，连接同一个pgsql和redis，最后查到由于harbor安装时两个实例上分别执行了install.sh脚本，自动生成的保存在本地harbor-data下面的secret/下面的密钥文件不一致导致这个问题出现

**解决**：将其中一个实例下的secret目录覆盖到另一个实例下，重启这个实例的harbor

#### harbor登录失败

**报错**

```shell
$ docker login -u admin -p Harbor12345 reg.mydomain.com
Error response from daemon: Get https://reg.mydomain.com/v2/: unauthorized: authentication required
```
**解决**
```plain
在harbor2.x当中如果需要使用nginx做代理，官方提供了external_url字段解决了这个问题，只需要在harbor.yml文件当中配置这个字段值即可。
eg:
hostname： reg.mydomain.com
external_url： https://reg.mydomain.com

修改后 docker-compose 重启即可
```
#### IOwait高问题排查

大概按这个步骤排查一下：

1、iotop ：查看io高的进程pid

2、iostat -x 1 ： 查看各磁盘负载 ，确认下是否是本地磁盘IO负载高

3、lsof -p pid ：查看进程打开的文件，看看是否在往后端存储写入

4、strace -o output.txt -T -tt -e trace=all -p pid ：追踪系统调用，具体文件fd



