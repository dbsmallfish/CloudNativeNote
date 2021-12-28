>官网文档：[https://docs.docker.com/engine/reference/run/](https://docs.docker.com/engine/reference/run/)
### **1、查看docker版本信息和系统信息**

1）查看docker版本信息

```shell
docker version
```
2）查看docker的系统信息
```shell
docker info
```
### **2、镜像查看/搜索/删除**

1）在线查找镜像(image)

```shell
docker search ImageName
```
2）列出本地所有镜像(image)
```shell
docker images [OPTIONS] [REPOSITORY[:TAG]]
OPTIONS说明：
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
--digests :显示镜像的摘要信息；
-f :显示满足条件的镜像；
--format :指定返回值的模板文件；
--no-trunc :显示完整的镜像信息；
-q :只显示镜像ID。
```
3）下载最新版镜像(image)或指定版本
```shell
docker pull ImageName 或 docker pull ImageName:VersionNumber
```
4）删除本地镜像(image), -f : 强制删除。
```shell
docker rmi ImageName
```
或
```shell
docker rmi ImageName:VersionNumber
```
### **3、启动容器**

**run:启动容器，如果本地没有要启动的镜像，会自动下载,每次新建一个容器。**

**start:只是单纯的启动, 不会新建容器**

```shell
docker run ImageName
docker start Name or ID
```
1）启动容器(container)，并在容器中运行"echo"命令，输出"hello world"。
```shell
docker run ImageName echo "hello world"
```
2）交互式进入容器中：
```shell
docker run -it ImageName /bin/bash
```
3）启动容器，并在容器中安装新的程序,-y 一定要加，因为在docker环境无法响应这种交互操作：
```shell
docker run ImageName apt-get install -y AppName
```
### **4、查看容器**

1）列出当前正在运行的所有容器：

```shell
docker ps
```
2）列出所有容器，不管是否在运行：
```shell
docker ps -a
```
3）列出最后一次启动的容器：
```shell
docker ps -l
```
### **5、容器(container)常用操作命令**

1）启动、停止、杀死一个容器：

```shell
docker start Name/ID
docker stop Name/ID
docker kill Name/ID
```
2）删除所有容器：
```shell
docker rm `docker ps -a -q`
```
3）删除单个容器
```shell
docker rm Name/ID
```
4）查看一个容器中的日志：
```shell
docker logs Name/ID
```
5）列出一个容器里面被改变的文件或者目录
list列表会显示出三种事件，A：增加的，D： 删除的，C： 被改变的 

```shell
docker diff Name/ID
```
6）显示一个运行的容器的进程信息
```shell
docker top Name/ID
```
7）从容器里面拷贝文件/目录到本地一个路径 
```shell
 docker cp Name:/container_path to_path docker cp ID:/container_path to_path
```
8）当重启一个正在运行的容器时，-t：关闭容器的限时，如果超时未能关闭则用kill强制关闭，默认值10s，这个时间用于容器的自己保存状态。
```shell
docker restart Name/ID
```

9）连接到正在运行中的容器，--sig-proxy=false ：此选项作用是按CTRL-D或CTRL-C不会关闭容器。

```shell
docker attach --sig-proxy=false Name/ID
```
### **6、commit镜像**

当你对某一个容器做了修改之后（通过在容器中运行某一个命令），可以把对容器的修改保存下来，这样下次可以从保存后的最新状态运行该容器。

```shell
docker commit containerID new_image_name
-a :提交的镜像作者；
-c :使用Dockerfile指令来创建镜像；
-m :提交时的说明文字；
-p :在commit时，将容器暂停。
```
例如：
```shell
docker commit -a "cjavapy.com" -m "my apache" 93c8176de0d6 myvm:v1
```
### **7、导出/导入镜像**

当需要把一台机器上的镜像迁移到另一台机器的时候，需要保存镜像与加载镜像。

1）保存镜像到一个tar包:

```shell
docker save image_name -o file_path
```
2）加载一个tar包格式的镜像:
```shell
docker load -i file_path
```
### **8、登录镜像仓库**

```shell
docker login [-e|-email=""] [-p|--password=""] [-u|--username=""] [SERVER]
```
例如：
```shell
docker login 192.168.31.55:5000
```
### **9、推送镜像**

```shell
docker push new_image_name
```
### **10、构建镜像**

```shell
docker build -t image_name Dockerfile_path
选项说明：
--build-arg=[]：设置镜像创建时的变量；
--cpu-shares：设置 cpu 使用权重；
--cpu-period：限制 CPU CFS周期；
--cpu-quota：限制 CPU CFS配额；
--cpuset-cpus：指定使用的CPU id；
--cpuset-mems：指定使用的内存 id；
--disable-content-trust：忽略校验，默认开启；
-f：指定要使用的Dockerfile路径；
--force-rm：设置镜像过程中删除中间容器；
--isolation：使用容器隔离技术；
--label=[]：设置镜像使用的元数据；
-m：设置内存最大值；
--memory-swap：设置Swap的最大值为内存+swap，"-1"表示不限swap；
--no-cache：创建镜像的过程不使用缓存；
--pull：尝试去更新镜像的新版本；
--quiet, -q：安静模式，成功后只输出镜像 ID；
--rm：设置镜像成功后删除中间容器；
--shm-size：设置/dev/shm的大小，默认值是64M；
--ulimit：Ulimit配置。
--tag, -t：
镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
--network:：默认 default。在构建期间设置RUN指令的网络模式
```

